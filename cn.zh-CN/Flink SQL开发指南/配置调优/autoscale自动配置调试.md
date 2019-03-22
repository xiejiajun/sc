# autoscale自动配置调试 {#concept_nc3_3qd_2hb .concept}

为了解决客户使用autoconf自动配置调优功能时需频繁的启停作业的问题，在blink3.x版本提供了autoscale自动配置调优功能。作业启动后，系统会根据资源配置规则，自动进行作业的调优，直到满足设定的调优目标，全程无需人工介入。

**说明：** autoscale自动配置调优功能仅支持blink3.x及以上版本。

## 资源配置方法 {#section_u5f_ky2_2hb .section}

在作业上线时**资源配置方式**选择**作业自动调优**即进入autoscale自动配置调试设置， 如下图。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155323953141342_zh-CN.png)

-   最大CU数

    设置作业允许使用的最大CU数，最大CU数需小于项目可用CU数。

-   调优策略

    系统会根据选择的策略和期望值对作业自动调优以达到期望值（目前策略只支持数据滞留时间）。

-   期望值

    预期选定的策略下作业需要达到的策略值。

-   初始资源
    -   系统分配

        初次上线作业使用系统默认配置，已上线作业使用最后一次调优后的配置，更多自定义资源需求可在手动配置资源中设置

    -   手动资源配置

        手动获取并修改作业启动的资源配置，需要您已经在作业属性中的手动资源配置页面配置完成


例如：调优策略设置数据滞留时间的期望值：5（秒）。假如，此时作业的数据滞留时间大于5秒，系统会不断进行自动调优，直至数据滞留时间降至小于5秒。

## 查看自动配置调优数据曲线 {#section_pmg_21f_2hb .section}

完成资源配置，进行作业上线、启动后，即可通过以下曲线，查看自动配置调优的状态。

-   AutoScale的成功和失败数

    记录整个Job进行AutoScale调优成功和失败的次数。 作业按系统计算出的所需资源启动定义为成功，未按照系统计算出的所需资源启动定义为失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155323953141345_zh-CN.png)

-   AutoScale使用的CPU

    记录Job进行AutoScale调优时，所申请的CPU总量。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155323953141346_zh-CN.png)

-   AutoScale使用的MEM

    记录Job进行AutoScale调优时，所申请的MEM总量。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155323953141347_zh-CN.png)


## FAQ {#section_psn_fcf_2hb .section}

Q：作业运行中怎么开启或关闭AutoScale？开启或关闭需要重启作业吗？

A：通过**运维**界面右侧的**作业列表**找到对应作业，作业中有动态开关（您可自行根据需求选择开启或关闭）。开启或关闭不需要重启作业 。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/147766/155323953141367_zh-CN.png)

Q：AutoScale失败对作业有什么影响，会一直失败吗？

A：AutoScale失败作业会报出failover，不会一直失败，只会是一段时间失败。AutoScale会根据上次调优失败的配置再次进行调优。

