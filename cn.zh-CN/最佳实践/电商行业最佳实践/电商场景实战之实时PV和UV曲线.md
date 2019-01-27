# 电商场景实战之实时PV和UV曲线 {#concept_65669_zh .concept}

本文以实时计算合作伙伴格格家的案例为您介绍如何使用实时计算制作实时PV/UV曲线图。

## 背景 {#section_ich_bq2_2gb .section}

随着新零售的概念慢慢崛起，互联网电商行业竞争越来越激烈。实时数据信息对于电商行业尤为重要，比如实时统计您对网页PV、UV的总量。

## 案例 {#section_stq_fq2_2gb .section}

-   业务架构图

    ![业务架构图](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41088/154857802134588_zh-CN.png)

-   业务流程
    1.  通过大数据总线（DataHub）提供的SDK把Binlog日志的数据同步到大数据总线（DataHub）。
    2.  阿里云实时计算订阅大数据总线（DataHub）的数据进行实时计算。
    3.  将实时数据插入到RDS云数据库。
    4.  再通过阿里云的DataV或者是其他的大屏做数据展示。
-   准备工作

    日志源表

    |字段名|数据类型|详情|
    |---|----|--|
    |account\_id|varchar|用户ID|
    |client\_ip|varchar|客户端IP|
    |client\_info|varchar|设备机型信息|
    |platform|varchar|系统版本信息|
    |imei|varchar|设备唯一标识|
    |version|bigint|版本号|
    |action|bigint|页面跳转描述|
    |gpm|varchar|埋点链路|
    |c\_time|varchar|请求时间|
    |target\_type|varchar|目标类型|
    |target\_id|varchar|目标ID|
    |udata|varchar|扩展信息|
    |session\_id|varchar|会话ID|
    |product\_id\_chain|varchar|商品ID串|
    |cart\_product\_id\_chain|varchar|加购商品ID|
    |tag|varchar|特殊标记|
    |position|varchar|位置信息|
    |network|varchar|网络使用情况|
    |p\_dt|varchar|时间分区|
    |p\_platform|varchar|系统版本信息|

    数据库RDS结果表

    |字段名|数据类型|详情|
    |---|----|--|
    |summary\_date|bigint|统计日期|
    |summary\_min|varchar|统计分钟|
    |pv|bigint|点击量|
    |uv|bigint|访客量**说明：** 一天内同个访客多次访问仅计算一个UV。

|
    |currenttime|timestamp|当前时间|

-   业务逻辑

    ```language-SQL
    
     --数据的订单源表
     
    CREATE TABLE source_ods_fact_log_track_action (
    	account_id                        VARCHAR,--用户ID
    	client_ip                         VARCHAR,--客户端IP
    	client_info                       VARCHAR,--设备机型信息
    	platform                          VARCHAR,--系统版本信息
    	imei                              VARCHAR,--设备唯一标识
    	`version`                         VARCHAR,--版本号
    	`action`                          VARCHAR,--页面跳转描述
    	gpm                               VARCHAR,--埋点链路
    	c_time                            VARCHAR,--请求时间
    	target_type                       VARCHAR,--目标类型
    	target_id                         VARCHAR,--目标ID
    	udata                             VARCHAR,--扩展信息，JSON格式
    	session_id                        VARCHAR,--会话ID
    	product_id_chain                  VARCHAR,--商品ID串
    	cart_product_id_chain             VARCHAR,--加购商品ID
    	tag                               VARCHAR,--特殊标记
    	`position`                        VARCHAR,--位置信息
    	network                           VARCHAR,--网络使用情况
    	p_dt                              VARCHAR,--时间分区天
    	p_platform                        VARCHAR--系统版本信息
    
      
    ) WITH (
    	type='datahub',
      	endPoint='xxxxxx',
        project='xxxxxx',
        topic='xxxxxx',
        accessId='xxxxxx',
        accessKey='xxxxxx',
    	batchReadSize='1000'
    );
    
    
    
    CREATE TABLE result_cps_total_summary_pvuv_min (
    	summary_date              bigint,--统计日期
    	summary_min               varchar,--统计分钟
    	pv                        bigint,--点击量
    	uv                        bigint,--一天内同个访客多次访问仅计算一个UV
    	currenttime               timestamp,--当前时间
    	primary key (summary_date,summary_min)
    ) WITH (
    	type= 'rds',
    	url = 'xxxxxx',
    	userName = 'xxxxxx',
    	password = 'xxxxxx',
    	tableName = 'xxxxxx'
    );
    
    CREATE VIEW result_cps_total_summary_pvuv_min_01 AS
    select 
    cast(p_dt as bigint) as summary_date --时间分区
    ,count(client_ip) as pv --客户端的IP
    ,count(distinct client_ip) as uv--客户端去重
    ,cast(max(c_time ) as TIMESTAMP)  as c_time--请求的时间
    from source_ods_fact_log_track_action
    group by p_dt;
    
    INSERT into  result_cps_total_summary_pvuv_min
    select 
    a.summary_date,--时间分区
    cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min,--取出小时分钟级别的时间
    a.pv,
    a.uv,
    CURRENT_TIMESTAMP  as currenttime--当前时间
    from result_cps_total_summary_pvuv_min_01 AS a
    ;
    
    ```

-   难点解析

    为了方便大家理解结构化代码和代码维护，我们推荐使用View（[数据视图概念](../../../../../cn.zh-CN/使用指南/Flink SQL/创建数据视图.md#)）把业务逻辑差分成二个模块。

    -   模块一

        ```language-SQL
        
        CREATE VIEW result_cps_total_summary_pvuv_min_01 AS
        select 
        cast(p_dt as bigint) as summary_date --时间分区
        ,count(client_ip) as pv --客户端的IP
        ,count(distinct client_ip) as uv--客户端去重
        ,cast(max(c_time) as TIMESTAMP)  as c_time--请求的时间
        from source_ods_fact_log_track_action
        group by p_dt;
        	 
        
        ```

        -   查出客户的IP访问网站的点击次数作为PV。对客户的IP去重作为UV。
        -   `cast(max(c_time) as TIMESTAMP)`，记录最后请求的时间。
        -   这段业务逻辑根据`p_dt`（时间分区，以`天`为单位）的时间来做分组，以`max(c_time)`表示一段时间的最后访问时间为截止，向数据库插入一条PV、UV的数量。
        结果如下表。

        |p\_dt|pv|uv|`max(c_time)`|
        |-----|--|--|-------------|
        |2017-12-12|1000|100|`2017-12-12 9:00:00`|
        |2017-12-12|1500|120|`2017-12-12 9:01:00`|
        |2017-12-12|2200|200|`2017-12-12 9:02:00`|
        |2017-12-12|3300|320|`2017-12-12 9:03:00`|

    -   模块二

        ```language-SQL
        INSERT into  result_cps_total_summary_pvuv_min
        select 
        a.summary_date,--时间分区,天为单位
        cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min,--取出小时分钟级别的时间
        a.pv,
        a.uv,
        CURRENT_TIMESTAMP  as currenttime--当前时间
        from result_cps_total_summary_pvuv_min_01 AS a
        
        
        ```

        把模块一的数据精确到小时分钟级别取出，最后根据数据得出PV、UV的增长曲线。如图所示：

        ![22](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41088/154857802134589_zh-CN.png)


