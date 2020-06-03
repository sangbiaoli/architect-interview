##dubbo

1. dubbo是什么

    dubbo是一个分布式框架，远程服务调用的分布式框架，其核心部分包含：
    * 集群容错：提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，地址路由，动态配置等集群支持。
    * 远程通讯：提供对多种基于长连接的NIO框架抽象封装，包括多种线程模型，序列化，以及“请求-响应”模式的信息交换方式
    * 自动发现：基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。

2. dubbo能做什么

    透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入。软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。

    服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。

3. 默认使用的是什么通信框架，还有别的选择吗?

    默认也推荐使用netty框架，还有mina。

4. 服务调用是阻塞的吗？

    默认是阻塞的，可以异步调用，没有返回值的可以这么做。

5. 一般使用什么注册中心？还有别的选择吗？

    推荐使用zookeeper注册中心，还有Multicast注册中心,Redis注册中心,Simple注册中心.

    ZooKeeper的节点是通过像树一样的结构来进行维护的，并且每一个节点通过路径来标示以及访问。除此之外，每一个节点还拥有自身的一些信息，包括：数据、数据长度、创建时间、修改时间等等。

6. 默认使用什么序列化框架，你知道的还有哪些？

    默认使用Hessian序列化，还有Duddo、FastJson、Java自带序列化。hessian是一个采用二进制格式传输的服务框架，相对传统soapwebservice，更轻量，更快速。

    Hessian原理与协议简析：

    http的协议约定了数据传输的方式，hessian也无法改变太多：

    1. hessian中client与server的交互，基于http-post方式。

    2. hessian将辅助信息，封装在httpheader中，比如“授权token”等，我们可以基于http-header来封装关于“安全校验”“meta数据”等。hessian提供了简单的”校验”机制。

    3. 对于hessian的交互核心数据，比如“调用的方法”和参数列表信息，将通过post请求的body体直接发送，格式为字节流。

    4. 对于hessian的server端响应数据，将在response中通过字节流的方式直接输出。

    hessian的协议本身并不复杂，在此不再赘言；所谓协议(protocol)就是约束数据的格式，client按照协议将请求信息序列化成字节序列发送给server端，server端根据协议，将数据反序列化成“对象”，然后执行指定的方法，并将方法的返回值再次按照协议序列化成字节流，响应给client，client按照协议将字节流反序列话成”对象”。

7. 服务提供者能实现失效踢出是什么原理？

    服务失效踢出基于zookeeper的临时节点原理。

8. 服务上线怎么不影响旧版本？

    采用多版本开发，不影响旧版本。在配置中添加version来作为版本区分

9. 如何解决服务调用链过长的问题？

    可以结合zipkin实现分布式服务追踪。

10. 说说核心的配置有哪些？

    核心配置有

    * dubbo:service/

    * dubbo:reference/

    * dubbo:protocol/

    * dubbo:registry/

    * dubbo:application/

    * dubbo:provider/

    * dubbo:consumer/

    *dubbo:method/

11. dubbo推荐用什么协议？

    默认使用dubbo协议。

12. 同一个服务多个注册的情况下可以直连某一个服务吗？

    可以直连，修改配置即可，也可以通过telnet直接某个服务。

13. dubbo在安全机制方面如何解决的？

    dubbo通过token令牌防止用户绕过注册中心直连，然后在注册中心管理授权，dubbo提供了黑白名单，控制服务所允许的调用方。

14. 集群容错怎么做？

    读操作建议使用Failover失败自动切换，默认重试两次其他服务器。写操作建议使用Failfast快速失败，发一次调用失败就立即报错。

15. 在使用过程中都遇到了些什么问题？如何解决的？

    1. 同时配置了XML和properties文件，则properties中的配置无效

        解决：只有XML没有配置时，properties才生效。

    2. dubbo缺省会在启动时检查依赖是否可用，不可用就抛出异常，阻止spring初始化完成，check属性默认为true。

        解决：测试时有些服务不关心或者出现了循环依赖，将check设置为false

    3. 为了方便开发测试，线下有一个所有服务可用的注册中心，这时，如果有一个正在开发中的服务提供者注册，可能会影响消费者不能正常运行。

        解决：让服务提供者开发方，只订阅服务，而不注册正在开发的服务，通过直连测试正在开发的服务。设置dubbo:registry标签的register属性为false。

    4. spring2.x初始化死锁问题。

        在spring解析到dubbo:service时，就已经向外暴露了服务，而spring还在接着初始化其他bean，如果这时有请求进来，并且服务的实现类里有调用applicationContext.getBean()的用法。getBean线程和spring初始化线程的锁的顺序不一样，导致了线程死锁，不能提供服务，启动不了。

        解决：不要在服务的实现类中使用applicationContext.getBean();如果不想依赖配置顺序，可以将dubbo:provider的deplay属性设置为-1，使dubbo在容器初始化完成后再暴露服务。

    5. 服务注册不上

        检查dubbo的jar包有没有在classpath中，以及有没有重复的jar包

        检查暴露服务的spring配置有没有加载

        在服务提供者机器上测试与注册中心的网络是否通

    6. 出现RpcException:No provider available for remote service异常

        表示没有可用的服务提供者

        1. 检查连接的注册中心是否正确

        2. 到注册中心查看相应的服务提供者是否存在

        3. 检查服务提供者是否正常运行

    7. 出现”消息发送失败”异常

        通常是接口方法的传入传出参数未实现Serializable接口。

16. dubbo和dubbox之间的区别？

    dubbox是当当网基于dubbo上做了一些扩展，如加了服务可restful调用，更新了开源组件等。

17. 你还了解别的分布式框架吗？

    别的还有spring的springcloud，facebook的thrift，twitter的finagle等。

18. Dubbo支持哪些协议，每种协议的应用场景，优缺点？

    * dubbo：单一长连接和NIO异步通讯，适合大并发小数据量的服务调用，以及消费者远大于提供者。传输协议TCP，异步，Hessian序列化；

    * rmi：采用JDK标准的rmi协议实现，传输参数和返回参数对象需要实现Serializable接口，使用java标准序列化机制，使用阻塞式短连接，传输数据包大小混合，消费者和提供者个数差不多，可传文件，传输协议TCP。多个短连接，TCP协议传输，同步传输，适用常规的远程服务调用和rmi互操作。在依赖低版本的Common-Collections包，java序列化存在安全漏洞；

    * webservice:基于WebService的远程调用协议，集成CXF实现，提供和原生WebService的互操作。多个短连接，基于HTTP传输，同步传输，适用系统集成和跨语言调用；

    * http：基于Http表单提交的远程调用协议，使用Spring的HttpInvoker实现。多个短连接，传输协议HTTP，传入参数大小混合，提供者个数多于消费者，需要给应用程序和浏览器JS调用；
    
    * hessian：集成Hessian服务，基于HTTP通讯，采用Servlet暴露服务，Dubbo内嵌Jetty作为服务器时默认实现，提供与Hession服务互操作。多个短连接，同步HTTP传输，Hessian序列化，传入参数较大，提供者大于消费者，提供者压力较大，可传文件；

    * memcache：基于memcached实现的RPC协议
    
    * redis：基于redis实现的RPC协议

19. Dubbo集群的负载均衡有哪些策略

    Dubbo提供了常见的集群策略实现，并预扩展点予以自行实现。

    1. RandomLoadBalance:随机选取提供者策略，有利于动态调整提供者权重。截面碰撞率高，调用次数越多，分布越均匀；

    2. RoundRobinLoadBalance:轮循选取提供者策略，平均分布，但是存在请求累积的问题；

    3. LeastActiveLoadBalance:最少活跃调用策略，解决慢提供者接收更少的请求；ConstantHashLoadBalance:一致性Hash策略，使相同参数请求总是发到同一提供者，一台机器宕机，可以基于虚拟节点，分摊至其他提供者，避免引起提供者的剧烈变动；

20. 服务调用超时问题怎么解决

    dubbo在调用服务不成功时，默认是会重试两次的。这样在服务端的处理时间超过了设定的超时时间时，就会有重复请求，比如在发邮件时，可能就会发出多份重复邮件，执行注册请求时，就会插入多条重复的注册数据，那么怎么解决超时问题呢？如下

    ```xml
    <dubbo:provider delay="-1" timeout="6000" retries="0"/>
    ```

    对于核心的服务中心，去除dubbo超时重试机制，并重新评估设置超时时间。业务处理代码必须放在服务端，客户端只做参数验证和服务调用，不涉及业务流程处理全局配置实例。