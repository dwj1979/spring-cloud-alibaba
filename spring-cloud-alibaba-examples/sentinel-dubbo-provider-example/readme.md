# Sentinel Dubbo Provider Example
## Project Instruction

This example illustrates how to use Sentinel starter to implement flow control for Spring Cloud applications.

[Sentinel](https://github.com/alibaba/Sentinel) is an open-source project of Alibaba. Sentinel takes "traffic flow" as the breakthrough point, and provides solutions in areas such as flow control, concurrency, circuit breaking, and load protection to protect service stability.

[Dubbo](http://dubbo.apache.org/) is a high-performance, java based open source RPC framework.

This example work with 'sentinel-dubbo-consumer-example' module and `sentinel-dubbo-provider-example` module should startup firstly.

This example focus on the integration of Sentinel and Dubbo. You can see more features on [sentinel-example](https://github.com/spring-cloud-incubator/spring-cloud-alibaba/tree/master/spring-cloud-alibaba-examples/sentinel-example).

## Demo

### Connect to Sentinel
Before we start the demo, let's learn how to connect Sentinel with Dubbo to a Spring Cloud application.
**Note: This section is to show you how to connect to Sentinel. The configurations have been completed in the following example, so you don't need modify the code any more.**

1. Add dependency spring-cloud-starter-sentinel and dubbo-spring-boot-starter in the pom.xml file in your Spring Cloud project.

	    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sentinel</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>
		  
2. Configure flow control rules 
	
	Sentinel provide [sentinel-dubbo-adapter](https://github.com/alibaba/Sentinel/tree/master/sentinel-adapter/sentinel-dubbo-adapter) module to support dubbo. to support dubbo. sentinel-starter integrates this feature by default.
    	
    sentinel-dubbo-adapter will using Sentinel to handle resource by Dubbo Filter. You just need to define rules.

        FlowRule flowRule = new FlowRule();
        flowRule.setResource("dubboResource");
        flowRule.setCount(10);
        flowRule.setGrade(RuleConstant.FLOW_GRADE_QPS);
        flowRule.setLimitApp("default");
        FlowRuleManager.loadRules(Collections.singletonList(flowRule));

### Configure and Publish Service

Define some configs of dubbo in `application.properties`, like protocol, config registry：

    spring.application.name = dubbo-provider-demo
    
    foo.service.version = 1.0.0
    
    dubbo.scan.basePackages = org.springframework.cloud.alibaba.cloud.examples
    
    dubbo.application.id = dubbo-provider-demo
    dubbo.application.name = dubbo-provider-demo
    
    dubbo.protocol.id = dubbo
    dubbo.protocol.name = dubbo
    dubbo.protocol.port = 12345
    dubbo.protocol.status = server
    
    dubbo.registry.id = my-registry
    dubbo.registry.address = N/A


`sentinel-dubbo-api` define a service named FooService:

    package org.springframework.cloud.alibaba.cloud.examples.FooService;
    public interface FooService {
        String hello(String name);
    }

Define the implement Service annotated by `@Service`:

    @Service(
            version = "${foo.service.version}",
            application = "${dubbo.application.id}",
            protocol = "${dubbo.protocol.id}",
            registry = "${dubbo.registry.id}"
    )
    public class FooServiceImpl implements FooService {
    
        @Override
        public String hello(String name) {
            return "hello, " + name;
        }
    }

### Start Application

Start the application in IDE or by building a fatjar.

1. Start in IDE: Find main class  `SentinelDubboProviderApp`, and execute the main method.
2. Build a fatjar：Execute command `mvn clean package` to build a fatjar，and run command `java -jar sentinel-dubbo-provider-example.jar` to start the application.


