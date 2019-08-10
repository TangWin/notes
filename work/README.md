# 分布式调度框架  Nros-Job

## 设计思想

> 将调度行为抽象形成“调度中心”公共平台，而平台自身并不承担业务逻辑，“调度中心”负责发起调度请求。
>
> 将任务抽象成分散的JobHandler，交由“执行器”统一管理，“执行器”负责接收调度请求并执行对应的JobHandler中业务逻辑。
>
> 因此，“调度”和“任务”两部分可以相互解耦，提高系统整体稳定性和扩展性；



## 模块划分

> nros-job-admin  调度中心
>
> nros-job-core  公共Jar依赖
>
> nros-job-executor-samples  执行器示例



## 接入流程

可以参看Gitlab上Nros-job工程中执行器示例代码
[Nros-job-executor-samples](http://182.92.143.42/nros/nros-job/tree/master/nros-job-executor-samples)

### 1.添加maven依赖

```xml
        <dependency>
            <groupId>com.ztesoft.zsmart.nros.sbc</groupId>
            <artifactId>nros-job-core</artifactId>
            <version>2.1.1-SNAPSHOT</version>
        </dependency>
```



### 2.添加执行器配置

在配置文件中加入执行器配置：

```properties
### 调度中心部署跟地址 [选填]：如调度中心集群部署存在多个地址则用逗号分隔。执行器将会使用该地址进行"执行器心跳注册"和"任务结果回调"；为空则关闭自动注册；
xxl.job.admin.addresses=http://127.0.0.1:8080/nros-job-admin

### 执行器AppName [选填]：执行器心跳注册分组依据；为空则关闭自动注册
xxl.job.executor.appname=xxl-job-executor-sample

### 执行器IP [选填]：默认为空表示自动获取IP，多网卡时可手动设置指定IP，该IP不会绑定Host仅作为通讯实用；地址信息用于 "执行器注册" 和 "调度中心请求并触发任务"；
xxl.job.executor.ip=

### 执行器端口号 [选填]：小于等于0则自动获取；默认端口为9999，单机部署多个执行器时，注意要配置不同执行器端口；
xxl.job.executor.port=9999

### 执行器通讯TOKEN [选填]：非空时启用；
xxl.job.accessToken=

### 执行器运行日志文件存储磁盘路径 [选填] ：需要对该路径拥有读写权限；为空则使用默认路径；
xxl.job.executor.logpath=/data/applogs/xxl-job/jobhandler

### 执行器日志保存天数 [选填] ：值大于3时生效，启用执行器Log文件定期清理功能，否则不生效；
xxl.job.executor.logretentiondays=-1
```



### 3.配置执行器实例bean

执行器bean配置：

```java
@Bean(initMethod = "start", destroyMethod = "destroy")
public XxlJobSpringExecutor xxlJobExecutor() {
    logger.info(">>>>>>>>>>> xxl-job config init.");
    XxlJobSpringExecutor xxlJobSpringExecutor = new XxlJobSpringExecutor();
    xxlJobSpringExecutor.setAdminAddresses(adminAddresses);
    xxlJobSpringExecutor.setAppName(appName);
    xxlJobSpringExecutor.setIp(ip);
    xxlJobSpringExecutor.setPort(port);
    xxlJobSpringExecutor.setAccessToken(accessToken);
    xxlJobSpringExecutor.setLogPath(logPath);
    xxlJobSpringExecutor.setLogRetentionDays(logRetentionDays);
    logger.info(">>>>>>>>>>> xxl-job config init.");
    return xxlJobSpringExecutor;
}
```



### 4.编写job代码

* 继承接口：IJobHandler，重写execute方法
* 使用注解@JobHandler，value为该Job在调度中心的JobHandler值
* 使用XxlJobLogger添加执行日志

```java
@JobHandler(value = "demoJobHandler")
@Component
public class DemoJobHandler extends IJobHandler {

    @Override
    public ReturnT<String> execute(String param) throws Exception {
        XxlJobLogger.log("I'm notify, Hello World.");
        for (int i = 0; i < 5; i++) {
            XxlJobLogger.log("beat at:" + i);
            TimeUnit.SECONDS.sleep(2);
        }
        return SUCCESS;
    }

}
```



### 5.调度中心新建执行器

找相关负责人在调度中心为该任务新建执行器，然后根据需要在调度中心创建任务



## 使用须知

* 新建任务时，请务必关注 路由策略、阻塞处理策略、失败重试次数 这几项；
* 使用 “执行一次”功能时，若配置了“失败重试次数”，则如果失败了会进行重试（默认为10秒重试1次）；
* 集群部署时，若其中某台机器发生了如网络错误等原因，其他机器正常，可以配置 路由策略 为 “故障转移” 进行规避，该策略会进行心跳检测，规避一些如 网络不可达 等原因发生的异常；



To be continued