
## gateway相关类

以下对一些gateway的filter按执行优先级作简单分析，之后会针对某些类作详细解读。

- WeightCalculatorWebFilter   
通过WeightRoutePredicateFactory的config计算权重，并在请求时将相关权重选择得出一个routeId做访问控制。

- DispatcherHandler
Central dispatcher for HTTP request handlers/controllers.
文档将此类等同于MVC的DispatcherServlet

- RoutePredicateHandlerMapping
注意两个核心方法：
getHandlerInternal 
lookupRoute - 找到当前请求对应的route

- FilteringWebHandler
请求的filters启动执行

- AdaptCachedBodyGlobalFilter
清掉CACHED_REQUEST_BODY_KEY这个key，这个key是可能在predicate之类的为了重复读body所用的cache key

- GatewayMetricsFilter
以routeId为维度的请求耗时，response code的监控。

- ForwardPathFilter
对于还没路由的route并且协议是forward的route，重写请求路径。

- LoadBalancerClientFilter
负载filter，匹配lb协议，然后，采用默认的ribbon的分zone的轮询。而节点信息目前使用的nacos的serverList（没有塞zone信息，不支持ribbon的按zone负载）。
在上面两个filter改了GATEWAY_REQUEST_URL_ATTR的exchange属性，如果需要加些自定义逻辑，可关注下这个。

- NettyRoutingFilter
通过netty发送上面转换过的面向真实节点的请求。
可通过spring.cloud.gateway.httpclient前缀的配置，控制请求超时等httpclient的配置。

- NettyWriteResponseFilter
把NettyRoutingFilter中设置的exchange属性CLIENT_RESPONSE_CONN_ATTR（即响应数据）返回。

