# ribbon
### 简介
spring cloud 的介绍如下：
Ribbon is a client side load balancer which gives you a lot of control over the behaviour of HTTP and TCP clients. Feign already uses Ribbon, so if you are using @FeignClient then this section also applies.

Ribbon是Netflix发布的云中间层服务开源项目，其主要功能是提供客户端侧负载均衡算法。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。我们可以在配置文件中列出Load Balancer后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器，我们也很容易使用Ribbon实现自定义的负载均衡算法。
核心有三点：
服务发现，发现依赖服务的列表  
服务选择规则，在多个服务中如何选择一个有效服务  
服务监听，检测失效的服务，高效剔除失效服务 
 
### ribbon使用
1.添加依赖：
``` xml
	<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
	</dependency>
``` 
2.
``` java
 @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        RestTemplate template = new RestTemplate();
        SimpleClientHttpRequestFactory factory = (SimpleClientHttpRequestFactory) template.getRequestFactory();
        factory.setConnectTimeout(3000);
        factory.setReadTimeout(3000);
        return template;
    }	
    
    @Configuration
	@RibbonClient(name = "foo", configuration = FooConfiguration.class)
	public class TestConfiguration {
	}	
``` 

### java代码自定义ribbon配置
IClientConfig ribbonClientConfig: DefaultClientConfigImpl

IRule ribbonRule: ZoneAvoidanceRule

IPing ribbonPing: NoOpPing

ServerList<Server> ribbonServerList: ConfigurationBasedServerList

ServerListFilter<Server> ribbonServerListFilter: ZonePreferenceServerListFilter

ILoadBalancer ribbonLoadBalancer: ZoneAwareLoadBalancer
``` java
@Configuration
public class FooConfiguration {
    @Bean
    public IPing ribbonPing(IClientConfig config) {
        return new PingUrl();
    }
}
``` 
### 属性自定义ribbon配置
```yml
users:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.WeightedResponseTimeRule
```     
    NFLoadBalancerClassName: should implement ILoadBalancer

	NFLoadBalancerRuleClassName: should implement IRule
	
	NFLoadBalancerPingClassName: should implement IPing
	
	NIWSServerListClassName: should implement ServerList
	
	NIWSServerListFilterClassName should implement ServerListFilter
### 脱离Eureka 使用ribbon
