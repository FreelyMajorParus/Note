# Ribbon 是什么？
可以使用Ribbon完成客户端实现的负载均衡组件。

# Ribbon 基本使用
## 硬编码
```java
@Autowired
private LoadBalancerClient loadBalancerClient;

@GetMapping("/getUserInfoRandom")
public String getUserInfoRandom(String userId) {
    ServiceInstance services = loadBalancerClient.choose("spring-cloud-order-service");
    return restTemplate.getForObject("http://" + services.getHost() + ":" + services.getPort() + "/getByUserId?userId=" + userId, String.class);
}
```
通过调用loadBalancerClient来选取默认策略下的serviceInstance, 通过该instance来获取hosts和port进行请求。
## 注解
```java
@Bean
@LoadBalanced
public RestTemplate restTemplate(RestTemplateBuilder restTemplateBuilder) {
    return restTemplateBuilder.build();
}

@GetMapping("/getUserInfo")
public String getUserInfo(String userId) {
    // 他也有负载均衡的作用了
    //return restTemplate.getForObject("http://localhost:8080/getByUserId?userId=" + userId, String.class);
    return restTemplate.getForObject("http://spring-cloud-order-service/getByUserId?userId=" + userId, String.class);
}
```
# Ribbon 实现原理
## RestTemplate 的构造
首先我们应该考虑为什么@LoadBalanced会为RestTemplate赋予负载均衡的功能？ @LoadBalanced是SpringCloud-commons包内定义的，相当于给外部集成留了一个口子。那@LoadBalanced是如何作用到RestTemplate上的呢？
RestTemplate的继承体系如下 :
![RestTemplate](https://github.com/FreelyMajorParus/Note/blob/main/screenshots/DFC55ED9-D67D-42BD-8D56-50766E111F02.png)
