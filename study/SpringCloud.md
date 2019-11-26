## 模块及简介

* Spring Cloud Eureka	服务注册中心，管理各个服务之间通讯
  * Eureka Server	管理各个服务，专门给其他服务注册
  * Eureka Client      服务客户端
    * 服务消费者，对已经注册的其他服务进行消费
    * 服务提供者，向其他服务提供本身的服务
    * 注：可以存在只消费/只提供服务的情况，也存在既是消费者也是提供者的情况
  * 治理机制
    * 提供者
      * 服务注册
        * 在启动时通过REST请求，向注册中心进行注册
      * 服务续约
        * 注册以后，通过*心跳* 的方式，告诉注册中心：“我还活着”
      * 服务下线
        * 发送REST请求，告诉服务中心：“我下线了”
    * 消费者
      * 服务列表获取
        * 启动时，发送REST请求，获取已经注册的、未下线的服务清单
      * 服务调用
        * 通过服务名称，获取提供服务的具体实例信息（优先访问同处一个zone下的提供者）
    * 注册中心
      * 失效剔除
        * 每隔一段时间（默认60s）将服务清单中已超时未续约（默认90s）的服务剔除
      * 自我保护
        * EurekaServer 在运行期间，会统计心跳失败的比例在15分钟之内是否低于85%(通常由于网络不稳定导致)。 Eureka Server会将当前的实例注册信息保护起来， 让这些实例不会过期，尽可能保护这些注册信息
* Spring Cloud Ribbon    负载均衡
  * 客户端负载均衡（Ribbon）
    * 客户端从EurekaServer中获取服务清单，在发送请求的时候通过负载均衡算法，在多个服务器之间选择一个进行访问
    * Ribbon 支持负载均衡，默认策略为轮询，也可以自定义策略
      * 自定义策略：继承AbstractLoadBalancerRule类，重写public Server choose(ILoadBalancer lb, Object key)
      * 可以配置**重试**机制
  * 服务端负载均衡（Nginx）
    * 清单在服务端，由 Nginx 实现负载均衡
* Spring Cloud Hystrix  服务保护、服务监控
  *  “雪崩”：高并发场景下，很可能因为单个请求的延迟，导致所有请求出现延迟，使得服务处于负载饱和的状态，资源耗尽，最终导致整个分布式系统不可用
  * Hystrix 服务保护
    * 熔断器（失败快速返回）
      * 在某个请求发生故障时，通过熔断器的故障监控，直接返回一个错误响应而不是长时间的等待，避免了故障蔓延引起更大的风险
        * 熔断关键参数：
          *  
    * 线程池隔离（资源/依赖隔离）
      *  每个服务有自己独立的线程池，假设某个服务出现了延迟过高的情况，也不会拖累其他的服务，只会对该服务的调用产生影响
    *  请求合并、请求缓存……
  * Hystrix 服务监控
    * Hystrix 仪表盘，实时监控 Hystrix 的各项指标信息
      * 单个实例的监控
      * 集群监控
* Spring Cloud Feign
  * 整合了 Ribbon 、 Hystrix
  * 声明式的远程服务调用
* Spring Cloud Zuul
* Spring Cloud Config