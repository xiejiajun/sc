# API DEMO {#concept_dns_qgr_tgb .concept}

以下为公有云环境，使用实时计算的API方式来操作实时计算产品上的所有项目作业的DEMO。

```language-java
package sample;

import com.aliyuncs.AcsRequest;
import com.aliyuncs.AcsResponse;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.foas.model.v20181111.*;
import com.aliyuncs.http.FormatType;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;
import com.google.gson.Gson;

import java.util.HashMap;
import java.util.Map;

/**
 * 适用于公共云环境
 *
 * 作业的一般流程是：
 * 如果有依赖package的话，先创建package
 * 创建job->获取planjson->更新planjson到job->上线job->启动job->停止instance
 * instance的状态一般如下(流作业为例，括号里的为动作):
 * job上线->UNKNOWN[启动job]->WAITING[等待]->RUNNING[暂停]->PAUSED[恢复]->RUNNING[停止]->TERMINATED
 */
public class PublicSample {

    //填写购买的foas所在的regionId
    private static final String regionId = "cn-shanghai";

    //云账号ak
    private static final String accessId = "xxxxxxxxx";
    private static final String accessKey = "yyyyyyyyy";

    private static final String projectName = "your project name";
    private static final String jobName = "your job name";

    // 文件夹与linux文件系统格式相同，以 / 开始，表示根目录，只支持绝对路径，不支持相对路径
    private static final String folderName = "/新手任务";
    private static final String clusterId = "et2bts";//可以通过listProjectBindQueue接口来获得
    private static final String queueName = "root.default";//可以通过listProjectBindQueue接口来获得
    private static final String engineVersion = "current";
    private static final String packageName = "xxx.jar";

    private static final String sql =
            "--SQL\n" +
                    "--********************************************************************--\n" +
                    "--CreateTime: 2018-12-29 14:35:04\n" +
                    "--Comment: 请输入业务注释信息\n" +
                    "--********************************************************************--\n" +
                    "\n" +
                    "CREATE TABLE datahub_source (`name` INT, num VARCHAR) WITH (\n" +
                    "    type = 'random'\n" +
                    ");\n" +
                    "CREATE TABLE datahub_sink (`name` INT, cnt BIGINT) WITH (\n" +
                    "    type = 'PRINT'\n" +
                    ");\n" +
                    "INSERT INTO\n" +
                    "    datahub_sink\n" +
                    "SELECT\n" +
                    "    `name`,\n" +
                    "    COUNT (1)\n" +
                    "FROM\n" +
                    "    datahub_source\n" +
                    "GROUP BY\n" +
                    "    `name`";

    private static final String properties =
            "#checkpoint模式：EXACTLY_ONCE 或者 AT_LEAST_ONCE\n" +
                    "#blink.checkpoint.mode=EXACTLY_ONCE\n" +
                    "#checkpoint间隔时间，单位毫秒\n" +
                    "#blink.checkpoint.interval.ms=180000\n" +
                    "#rocksdb的数据生命周期，单位毫秒\n" +
                    "#state.backend.rocksdb.ttl.ms=129600000\n";

    private static <T extends AcsResponse> T getResponse(IAcsClient client, AcsRequest<T> request) {
        AcsResponse response = null;
        try {
            //必须设置为json
            request.setHttpContentType(FormatType.JSON);
            request.setAcceptFormat(FormatType.JSON);

            response = client.getAcsResponse(request);
        } catch (Exception e) {
            //处理自己的异常逻辑，请注意异常中的requestId字段，排查问题时，需要提供该值给服务端
            System.out.println(e.getMessage());
        }
        return (T) response;
    }

    public static void main(String[] args) throws Exception {

        IClientProfile profile = DefaultProfile.getProfile(regionId, accessId, accessKey);
        DefaultAcsClient client = new DefaultAcsClient(profile);
        client.setAutoRetry(false);//不重试

        //获取文件夹列表
        ListChildFolderRequest listChildFolderRequest = new ListChildFolderRequest();
        listChildFolderRequest.setPath("/");
        listChildFolderRequest.setProjectName(projectName);
        ListChildFolderResponse listChildFolderResponse = PublicSample.getResponse(client, listChildFolderRequest);
        System.out.println(new Gson().toJson(listChildFolderResponse));

        //获取文件夹
        GetFolderRequest getFolderRequest = new GetFolderRequest();
        getFolderRequest.setPath(folderName);
        getFolderRequest.setProjectName(projectName);
        GetFolderResponse getFolderResponse = PublicSample.getResponse(client, getFolderRequest);
        System.out.println(new Gson().toJson(getFolderResponse));

        Long folderId;
        GetFolderResponse.Folder folder = getFolderResponse.getFolder();

        if (folder == null || folder.getFolderId() == null) {
            //创建文件夹
            CreateFolderRequest createFolderRequest = new CreateFolderRequest();
            createFolderRequest.setPath(folderName);
            createFolderRequest.setProjectName(projectName);
            CreateFolderResponse createFolderResponse = PublicSample.getResponse(client, createFolderRequest);
            System.out.println(new Gson().toJson(createFolderResponse));
            folderId = createFolderResponse.getFolderId();
        } else {
            folderId = folder.getFolderId();
        }

        // sdk的package存在用户自己的oss上，此处需要将meta信息提供给foas，
        // 并对foas授权bucket读取权限(role:AliyunStreamDefaultRole)，foas在使用时会从用户指定的oss和bucket上下载package
        // 请自行保证oss的region与foas所在的region一致
        // 如果project建在小集群上，则不需要提供endpoint，bucket等信息，只需要提供package的ossPath即可，
        // 因为用户的oss信息在创建小集群的时候已经提供过了，不需要重复提供
        CreatePackageRequest createPackageRequest = new CreatePackageRequest();
        createPackageRequest.setProjectName(projectName);
        createPackageRequest.setPackageName(packageName);
        createPackageRequest.setType("jar");
        createPackageRequest.setDescription("test package");
        createPackageRequest.setOssEndpoint("oss-cn-hangzhou.aliyuncs.com");//提供的endpoint需要保证网络可通达
        createPackageRequest.setOssBucket("your bucket");
        createPackageRequest.setOssOwner("111111111");//oss的主账号uid
        createPackageRequest.setOssPath("aaa/bbb/ccc.jar");
        createPackageRequest.setMd5("123456");//可不填，不填的话就会跳过md5校验，提供了MD5的话，在下载时会做完整性校验
        CreatePackageResponse createPackageResponse = PublicSample.getResponse(client, createPackageRequest);
        System.out.println(new Gson().toJson(createPackageResponse));

        //更新package
        UpdatePackageRequest updatePackageRequest = new UpdatePackageRequest();
        updatePackageRequest.setProjectName(projectName);
        updatePackageRequest.setPackageName(packageName);
        updatePackageRequest.setOssPath("aaa/bbb/ddd.jar");
        UpdatePackageResponse updatePackageResponse = PublicSample.getResponse(client, updatePackageRequest);
        System.out.println(new Gson().toJson(updatePackageResponse));

        //获取package详情
        GetPackageRequest getPackageRequest = new GetPackageRequest();
        getPackageRequest.setProjectName(projectName);
        getPackageRequest.setPackageName(packageName);
        GetPackageResponse getPackageResponse = PublicSample.getResponse(client, getPackageRequest);
        System.out.println(new Gson().toJson(getPackageResponse));

        //根据条件搜索package
        ListPackageRequest listPackageRequest = new ListPackageRequest();
        listPackageRequest.setProjectName(projectName);
        listPackageRequest.setPackageName("aa");//模糊匹配
        listPackageRequest.setType("jar");
        listPackageRequest.setPageIndex(1);//可不填，默认1
        listPackageRequest.setPageSize(30);//可不填，默认10
        ListPackageResponse listPackageResponse = PublicSample.getResponse(client, listPackageRequest);
        System.out.println(new Gson().toJson(listPackageResponse));

        //列举project绑定的cluster和queue
        ListProjectBindQueueRequest listProjectBindQueueRequest = new ListProjectBindQueueRequest();
        listProjectBindQueueRequest.setProjectName(projectName);
        ListProjectBindQueueResponse listProjectBindQueueResponse = PublicSample.getResponse(client, listProjectBindQueueRequest);
        System.out.println(new Gson().toJson(listProjectBindQueueResponse));

        //查询project下绑定的queue的资源
        ListProjectBindQueueResourceRequest listProjectBindQueueResourceRequest = new ListProjectBindQueueResourceRequest();
        listProjectBindQueueResourceRequest.setProjectName(projectName);
        ListProjectBindQueueResourceResponse listProjectBindQueueResourceResponse = PublicSample.getResponse(client, listProjectBindQueueResourceRequest);
        System.out.println(new Gson().toJson(listProjectBindQueueResourceResponse));

        //获取作业
        GetJobRequest getJobRequest = new GetJobRequest();
        getJobRequest.setProjectName(projectName);
        getJobRequest.setJobName(jobName);
        GetJobResponse getJobResponse = PublicSample.getResponse(client, getJobRequest);
        System.out.println(new Gson().toJson(getJobResponse));

        GetJobResponse.Job job = getJobResponse.getJob();
        if (job == null || job.getJobName() == null) {
            //创建作业
            CreateJobRequest createJobRequest = new CreateJobRequest();
            createJobRequest.setProjectName(projectName);
            createJobRequest.setJobName(jobName);
            createJobRequest.setCode(sql);
            createJobRequest.setProperties(properties);//无则不用设置
            //clusterId和queueName可以不用配置，不配置，会随机从项目中绑定的queue中选择一个
            createJobRequest.setClusterId(clusterId);
            createJobRequest.setQueueName(queueName);
            //engineVersion可以不用配置，不配置，会使用current版本
            createJobRequest.setEngineVersion(engineVersion);
            createJobRequest.setDescription("test");
            createJobRequest.setPackages(packageName);//多个package之间用英文逗号分隔
            createJobRequest.setJobType("flink_stream");//flink_stream/flink_bacth 大小写不敏感
            createJobRequest.setApiType("sql");//datatream/sql 大小写不敏感
            createJobRequest.setFolderId(folderId);

            CreateJobResponse createJobResponse = PublicSample.getResponse(client, createJobRequest);
            System.out.println(new Gson().toJson(createJobResponse));
        } else {
            //修改作业
            UpdateJobRequest updateJobRequest = new UpdateJobRequest();
            updateJobRequest.setProjectName(projectName);
            updateJobRequest.setJobName(jobName);
            updateJobRequest.setCode(sql);//设置想要修改的字段，不修改的字段不用设置

            UpdateJobResponse updateJobResponse = PublicSample.getResponse(client, updateJobRequest);
            System.out.println(new Gson().toJson(updateJobResponse));
        }

        //查找指定的package被哪些job所引用，该接口查询的是上线后的job引用关系，未上线的job不会返回
        GetRefPackageJobRequest getRefPackageJobRequest = new GetRefPackageJobRequest();
        getRefPackageJobRequest.setProjectName(projectName);
        getRefPackageJobRequest.setPackageName(packageName);
        GetRefPackageJobResponse getRefPackageJobResponse = PublicSample.getResponse(client, getRefPackageJobRequest);
        System.out.println(new Gson().toJson(getRefPackageJobResponse));

        //校验job是否存在语法错误
        ValidateJobRequest validateJobRequest = new ValidateJobRequest();
        validateJobRequest.setProjectName(projectName);
        validateJobRequest.setJobName(jobName);
        ValidateJobResponse validateJobResponse = PublicSample.getResponse(client, validateJobRequest);
        System.out.println(new Gson().toJson(validateJobResponse));

        //获取job的planjson，该接口是异步接口，提交完成之后，需要调用下面的CheckRawPlanJson接口来获取结果:Session in run/success/fail
        GetRawPlanJsonRequest getRawPlanJsonRequest = new GetRawPlanJsonRequest();
        getRawPlanJsonRequest.setProjectName(projectName);
        getRawPlanJsonRequest.setJobName(jobName);
        getRawPlanJsonRequest.setAutoconfEnable(true);//可以不填，默认为false
        //设置这个job预期使用多少资源来运行，这个参数会影响到生成plan的大小，不提供的话，底层引擎会默认生成资源量
        getRawPlanJsonRequest.setExpectedCore(2f);//这个参数与下面的参数可以同时提供，也可以同时不提供，不允许一个提供，一个不提供
        getRawPlanJsonRequest.setExpectedGB(9f);//
        GetRawPlanJsonResponse getRawPlanJsonResponse = PublicSample.getResponse(client, getRawPlanJsonRequest);

        CheckRawPlanJsonRequest checkRawPlanJsonRequest = new CheckRawPlanJsonRequest();
        checkRawPlanJsonRequest.setProjectName(projectName);
        checkRawPlanJsonRequest.setJobName(jobName);
        checkRawPlanJsonRequest.setSessionId(getRawPlanJsonResponse.getSessionId());

        String planJson;
        CheckRawPlanJsonResponse checkRawPlanJsonResponse;
        while (true) {
            checkRawPlanJsonResponse = PublicSample.getResponse(client, checkRawPlanJsonRequest);
            System.out.println(checkRawPlanJsonResponse.getPlanJsonInfo().getStatus());
            if (checkRawPlanJsonResponse.getPlanJsonInfo().getStatus().equals("success")) {
                planJson = checkRawPlanJsonResponse.getPlanJsonInfo().getPlanJson();
                break;
            } else if (checkRawPlanJsonResponse.getPlanJsonInfo().getStatus().equals("fail")) {
                System.out.println(checkRawPlanJsonResponse.getPlanJsonInfo().getErrorMessage());
                return;
            }
            Thread.sleep(1000);//每秒查询一次，不用过快
        }

        //修改作业planjson
        UpdateJobRequest updateJobRequest = new UpdateJobRequest();
        updateJobRequest.setProjectName(projectName);
        updateJobRequest.setJobName(jobName);
        updateJobRequest.setPlanJson(planJson);
        UpdateJobResponse updateJobResponse = PublicSample.getResponse(client, updateJobRequest);
        System.out.println(new Gson().toJson(updateJobResponse));

        //上线作业
        CommitJobRequest commitJobRequest = new CommitJobRequest();
        commitJobRequest.setProjectName(projectName);
        commitJobRequest.setJobName(jobName);
        CommitJobResponse commitJobResponse = PublicSample.getResponse(client, commitJobRequest);
        System.out.println(new Gson().toJson(commitJobResponse));

        //获取实例
        GetInstanceRequest getInstanceRequest = new GetInstanceRequest();
        getInstanceRequest.setProjectName(projectName);
        getInstanceRequest.setJobName(jobName);

        //-1表示获取流作业的当前运行实例，注意批作业不可这样填，批作业需要填实际的运行实例id
        getInstanceRequest.setInstanceId(-1L);
        GetInstanceResponse getInstanceResponse = PublicSample.getResponse(client, getInstanceRequest);
        System.out.println(new Gson().toJson(getInstanceResponse));

        GetInstanceResponse.Instance instance = getInstanceResponse.getInstance();

        {
            //启动作业，实例状态机参考文档
            if (instance == null || "UNKNOWN".equals(instance.getActualState()) || "TERMINATED".equals(instance.getActualState())) {
                StartJobRequest startJobRequest = new StartJobRequest();
                startJobRequest.setProjectName(projectName);
                startJobRequest.setJobName(jobName);

                Map map = new HashMap<String, String>();
                //startOffset表示启动点位
                map.put("startOffset", String.valueOf(System.currentTimeMillis()));
                startJobRequest.setParameterJson(new Gson().toJson(map));//json格式参数
                StartJobResponse startJobResponse = PublicSample.getResponse(client, startJobRequest);
                System.out.println(new Gson().toJson(startJobResponse));

                //等待启动成功
                while (true) {
                    GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("WAITING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            // 作业启动成功后，可以调用如下的一些接口进行运维
            //获取instance的运行dag图
            GetInstanceDetailRequest getInstanceDetailRequest = new GetInstanceDetailRequest();
            getInstanceDetailRequest.setProjectName(projectName);
            getInstanceDetailRequest.setJobName(jobName);
            getInstanceDetailRequest.setInstanceId(-1L);
            GetInstanceDetailResponse getInstanceDetailResponse = PublicSample.getResponse(client, getInstanceDetailRequest);
            System.out.println(new Gson().toJson(getInstanceDetailResponse));

            //获取instance的运行配置
            GetInstanceConfigRequest getInstanceConfigRequest = new GetInstanceConfigRequest();
            getInstanceConfigRequest.setProjectName(projectName);
            getInstanceConfigRequest.setJobName(jobName);
            getInstanceConfigRequest.setInstanceId(-1L);
            GetInstanceConfigResponse getInstanceConfigResponse = PublicSample.getResponse(client, getInstanceConfigRequest);
            System.out.println(new Gson().toJson(getInstanceConfigResponse));

            //获取instance的checkpoint信息
            GetInstanceCheckpointRequest getInstanceCheckpointRequest = new GetInstanceCheckpointRequest();
            getInstanceCheckpointRequest.setProjectName(projectName);
            getInstanceCheckpointRequest.setJobName(jobName);
            getInstanceCheckpointRequest.setInstanceId(-1L);
            GetInstanceCheckpointResponse getInstanceCheckpointResponse = PublicSample.getResponse(client, getInstanceCheckpointRequest);
            System.out.println(new Gson().toJson(getInstanceCheckpointResponse));

            //获取instance的运行时异常信息(failover信息)
            GetInstanceExceptionsRequest getInstanceExceptionsRequest = new GetInstanceExceptionsRequest();
            getInstanceExceptionsRequest.setProjectName(projectName);
            getInstanceExceptionsRequest.setJobName(jobName);
            getInstanceExceptionsRequest.setInstanceId(-1L);
            GetInstanceExceptionsResponse getInstanceExceptionsResponse = PublicSample.getResponse(client, getInstanceExceptionsRequest);
            System.out.println(new Gson().toJson(getInstanceExceptionsResponse));

            //查询instance的运行时各项指标的数据曲线信息
            //当前只支持查询最近两小时的曲线，metric的格式为：blink.{projectName}.{jobName}.{metricName}
            String metricJson = "{\n" +
                    "\t\"start\": 1547637620000,\n" +
                    "\t\"limit\": \"avg:sample:50\",\n" +
                    "\t\"end\": 1547638420000,\n" +
                    "\t\"queries\": [{\n" +
                    "\t\t\"downsample\": \"20s-avg\",\n" +
                    "\t\t\"metric\": \"blink.bayes_team.huayuan_test_job.delay\",\n" +
                    "\t\t\"granularity\": \"20s\",\n" +
                    "\t\t\"aggregator\": \"max\"\n" +
                    "\t}, {\n" +
                    "\t\t\"downsample\": \"20s-avg\",\n" +
                    "\t\t\"metric\": \"blink.bayes_team.huayuan_test_job.fetched_delay\",\n" +
                    "\t\t\"granularity\": \"20s\",\n" +
                    "\t\t\"aggregator\": \"max\"\n" +
                    "\t}]\n" +
                    "}";

            GetInstanceMetricRequest getInstanceMetricRequest = new GetInstanceMetricRequest();
            getInstanceMetricRequest.setProjectName(projectName);
            getInstanceMetricRequest.setJobName(jobName);
            getInstanceMetricRequest.setInstanceId(-1L);
            getInstanceMetricRequest.setMetricJson(metricJson);
            GetInstanceMetricResponse getInstanceMetricResponse = PublicSample.getResponse(client, getInstanceMetricRequest);
            System.out.println(new Gson().toJson(getInstanceMetricResponse.getMetrics()));

            //获取instance实际使用的资源信息（cpu/memory）
            GetInstanceResourceRequest getInstanceResourceRequest = new GetInstanceResourceRequest();
            getInstanceResourceRequest.setProjectName(projectName);
            getInstanceResourceRequest.setJobName(jobName);
            getInstanceResourceRequest.setInstanceId(-1L);
            GetInstanceResourceResponse getInstanceResourceResponse = PublicSample.getResponse(client, getInstanceResourceRequest);
            System.out.println(new Gson().toJson(getInstanceResourceResponse.getResource()));

            //批量获取多个作业的运行时状态
            BatchGetInstanceRunSummaryRequest batchGetInstanceRunSummaryRequest = new BatchGetInstanceRunSummaryRequest();
            batchGetInstanceRunSummaryRequest.setProjectName(projectName);
            batchGetInstanceRunSummaryRequest.setJobType("flink_stream");
            batchGetInstanceRunSummaryRequest.setJobNames("job1,job2,job3");//多个job之间用英文逗号分隔，请确保都是stream job，并且job已存在且已上线，否则会忽略错误的job
            BatchGetInstanceRunSummaryResponse batchGetInstanceRunSummaryResponse = PublicSample.getResponse(client, batchGetInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(batchGetInstanceRunSummaryResponse.getRunSummarys()));
        }

        {
            //暂停作业，实例状态机参考文档
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("PAUSED");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待暂停成功
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            //恢复作业，实例状态机参考文档
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("PAUSED".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("RUNNING");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待恢复成功
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("PAUSED".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        {
            //停止作业，实例状态机参考文档
            GetInstanceRunSummaryRequest getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
            getInstanceRunSummaryRequest.setProjectName(projectName);
            getInstanceRunSummaryRequest.setJobName(jobName);
            getInstanceRunSummaryRequest.setInstanceId(-1L);
            GetInstanceRunSummaryResponse getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
            System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

            if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                ModifyInstanceStateRequest modifyInstanceStateRequest = new ModifyInstanceStateRequest();
                modifyInstanceStateRequest.setProjectName(projectName);
                modifyInstanceStateRequest.setJobName(jobName);
                modifyInstanceStateRequest.setInstanceId(-1L);
                modifyInstanceStateRequest.setExpectState("TERMINATED");
                ModifyInstanceStateResponse modifyInstanceStateResponse = PublicSample.getResponse(client, modifyInstanceStateRequest);
                System.out.println(new Gson().toJson(modifyInstanceStateResponse));

                //等待停止成功
                while (true) {
                    getInstanceRunSummaryRequest = new GetInstanceRunSummaryRequest();
                    getInstanceRunSummaryRequest.setProjectName(projectName);
                    getInstanceRunSummaryRequest.setJobName(jobName);
                    getInstanceRunSummaryRequest.setInstanceId(-1L);
                    getInstanceRunSummaryResponse = PublicSample.getResponse(client, getInstanceRunSummaryRequest);
                    System.out.println(new Gson().toJson(getInstanceRunSummaryResponse));

                    if ("RUNNING".equals(getInstanceRunSummaryResponse.getRunSummary().getActualState())) {
                        System.out.println(String.format("lastErrorTime[%s], lastErrorMessage[%s]",
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorTime(),
                                getInstanceRunSummaryResponse.getRunSummary().getLastErrorMessage()));
                        Thread.sleep(1000);
                    } else {
                        break;
                    }
                }
            }
        }

        //下线作业
        OfflineJobRequest offlineJobRequest = new OfflineJobRequest();
        offlineJobRequest.setProjectName(projectName);
        offlineJobRequest.setJobName(jobName);
        OfflineJobResponse offlineJobResponse = PublicSample.getResponse(client, offlineJobRequest);
        System.out.println(new Gson().toJson(offlineJobResponse));

        //删除作业
        DeleteJobRequest deleteJobRequest = new DeleteJobRequest();
        deleteJobRequest.setProjectName(projectName);
        deleteJobRequest.setJobName(jobName);
        DeleteJobResponse deleteJobResponse = PublicSample.getResponse(client, deleteJobRequest);
        System.out.println(new Gson().toJson(deleteJobResponse));

        //删除package
        DeletePackageRequest deletePackageRequest = new DeletePackageRequest();
        deletePackageRequest.setProjectName(projectName);
        deletePackageRequest.setPackageName(packageName);
        DeletePackageResponse deletePackageResponse = PublicSample.getResponse(client, deletePackageRequest);
        System.out.println(new Gson().toJson(deletePackageResponse));
    }
}
```

