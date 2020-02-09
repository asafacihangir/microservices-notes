# Building Microservices
The term “microservices” is used to describe a software architectural design whereby many loosely-coupled components run independently, but ultimately work together as a single application. Services typically focus on particular aspects of a business domain or business entities and they tyically use a network to communicate.[1] *(“Mikroservisler” terimi, loosely-coupled  birçok bileşenin bağımsız olarak çalıştığı, ancak sonuçta tek bir uygulama olarak birlikte çalıştığı bir yazılım mimari tasarımını tanımlamak için kullanılır. Servisler genellikle business domain veya business entity'lerin belirli yönlerine odaklanır ve tipik olarak aralarında iletişim kurmak için bir network kullanırlar.)*

Netflix became one of the earliest adopters of microservices, having transitioned their monolithic application to a horizontally scalable, distributed architecture long before the term “microservices” gained any sort of traction.[1] *(Netflix, “mikroservis” terimi herhangi bir popülarite kazanmadan çok önce, monolitik uygulamalarını yatay olarak ölçeklenebilir, dağıtılmış bir mimariye geçirmiş olan mikroservislerin ilk uygulayıcılarından biri oldu.)*

Over the years Netflix have open-sourced a number of tools (that they continue to use internally) as part of the Netflix Open Source Software Center (Netflix OSS) project. Many of these tools have been adopted by the Spring team as part of the Spring Cloud project, which provides tools to assist developers with some of the common patterns used when building distributed systems.[1]. *(Netflix, yıllar içinde (Netflix OSS) projesinin bir parçası olarak bazı araçlarını(dahili olarak kullanmaya devam ettikleri) açık kaynaklı hale getirmiştir. Bu araçların birçoğu, Spring ekibi tarafından; geliştiricilere, dağıtılmış sistemler oluştururken kullanılan bazı yaygın kalıplar konusunda yardımcı olacak araçlar sağlayan Spring Cloud projesinin bir parçası olarak benimsenmiştir.)*


# The Project
Let’s imagine a simple online store scenario, where customers can place orders. We can already identify some services that we’ll need - a customer service and an order service. We’ll take it step by step to build out each one.[1]

# Service Discovery
A microservice architecture can be incredibly dynamic. Services don’t necessarily have fixed addresses, known ahead of time. They can be moved around onto different ports, machines and even different data centres entirely. More often than not, there will be many instances of a given service - a number that is rarely constant as new instances are often introduced to meet demand and are removed when demand decreases. They also need to discover other services in order to communicate with them.[1] *(Bir mikroservis mimarisi inanılmaz derecede dinamik olabilir. Servislerin önceden bilinen sabit adresleri olması gerekmez. Bunlar tamamen farklı portlarda, makinelere ve hatta farklı veri merkezlerine taşınabilir. Sık olmamakla birlikte, belirli bir servisin birçok instance'ı olacaktır.Instance adedi nadiren sabit olan bir sayıdır.Çünkü talebi karşılamak için genellikle yeni instance'lar getirilir ve talep azaldığında bu instance'lar kaldırılır Ayrıca onlarla iletişim kurmak için başka hizmetleri de keşfetmeleri gerekir.)*

Netflix’s Eureka is a service discovery tool, designed to solve this problem. When a service starts up, it registers itself with Eureka, specifying its name, address and other relevant information. It regularly sends heartbeat messages to Eureka to communicate that it’s still alive and able to handle requests. If that heartbeat stops for any reason, Eureka will de-register that particular service after a configured timeout. Services can also request registry information from Eureka in order to discover other services.[1] *(Netflix’in Eureka, bu sorunu çözmek için tasarlanmış bir servis keşif aracıdır. Bir servis başladığında, adını, adresini ve diğer ilgili bilgileri belirterek Eureka'ya kaydolur. Eureka'ya, hala hayatta olduğunu ve istekleri yerine getirebildiğini bildirmek için düzenli olarak heartbeat mesajları gönderir. Bu heartbeat herhangi bir nedenle durursa, Eureka konfigure edilmiş bir zaman aşımından sonra söz konusu servisin kaydını silecektir. Servisler, diğer servisleri keşfetmek için Eureka'dan kayıt defteri bilgileri de isteyebilir)*


![Alt text](images/eureka-diagram.jpg?raw=true "Title")


# Code Explanation-Discovery Service
Open up the DiscoveryServiceApplication class and add the @EnableEurekaServer annotation, to stand up a Eureka service registry:
```java
@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(DiscoveryServiceApplication.class, args);
    }
}
```
By default, a Eureka server communicates with peers to share their registry information in order to provide high availability, but since we’re just going to run a single instance here, let’s disable that feature and configure our service not to register with a peer and not to fetch a peer registry. We’ll also give it a name and a default port of 3000 (the default for Eureka is 8761). In application.properties add the following:

```
spring.application.name=discovery-service
server.port=3000
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=false
eureka.instance.hostname=localhost
eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```

Build and run the service (./gradlew bootRun) and confirm that it works by visiting http://localhost:3000. You should see a Eureka dashboard which displays information about the running instance:

![Alt text](images/euraka-dashboard-1.png?raw=true "Title")
![Alt text](images/euraka-dashboard-2.png?raw=true "Title")



Eureka dashboard'da, "Instances currently registered with Eureka" kısmında kaydolmuş servisleri görebiliriz.

![Alt text](images/instances-currenty-registered-with-eureka.png?raw=true "Title")


# Code Explanation-Registering with the Discovery Service
Now our Discovery Service is up and running, our domain services must communicate with it to register themselves and to receive registry updates. To do this, we’ll need to add the Eureka Discovery Client dependency to our projects. 

Now, let’s annotate our CustomerServiceApplication and OrderServiceApplication classes with the @EnableEurekaClient annotation:

```java
@SpringBootApplication
@EnableEurekaClient
public class CustomerServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(CustomerServiceApplication.class, args);
    }
}
````

Finally, let’s tell the Eureka Client where to find our Discovery Service. In each service’s application.properties, add the following line:
```eureka.client.serviceUrl.defaultZone=http://localhost:3000/eureka/```

# Routing and Server-Side Load Balancing with Zuul

In a microservice architecture there can be tens, hundreds or even thousands of services. Many are private and internal, but some need to be exposed to the outside world. We need a single entry point into the system to allow us to wire up and expose selected services to the outside world.[1]

Netflix’s Zuul (a reference to the Gatekeeper of Gozer in Ghostbusters) is a JVM based router and server-side load balancer. By mapping routes to services via its configuration, Zuul can integrate with Eureka to discover service locations to load balance and proxy requests to them.[1]

Zuul also supports filters which allows developers to intercept requests before they are sent to services (Pre-filters) and responses before being sent back to clients (Post-filters). This enables developers to implement functionality that is common to all services, running either before or after requests are handled. Filters are often used for features such as authentication, load shedding and CORS management, to name just a few.[1]


![Alt text](images/zuul-diagram.jpg?raw=true "Title")


# Code Explantion Gateway Service
Back at start.spring.io, create a new project with the Artifact gateway-service with Zuul and Eureka Discovery Client as dependencies. Once generated, open up the GatewayServiceApplication class and add the @EnableZuulProxy and @EnableEurekaClient annotations.[1]

```java
@SpringBootApplication
@EnableZuulProxy
@EnableEurekaClient
public class GatewayServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayServiceApplication.class, args);
    }
}
```
Now add the following to application.properties:

```
spring.application.name=gateway-service
server.port=80
eureka.client.serviceUrl.defaultZone=http://localhost:3000/eureka/

zuul.routes.customers.path=/customers/**
zuul.routes.customers.serviceId=customer-service

zuul.routes.orders.path=/orders/**
zuul.routes.orders.serviceId=order-service
```

We’ve given the project a name of gateway-service, set it to run on port 80 (which is the default for HTTP) and told it to find our Discovery Service on port 3000. We’ve also added some route mappings so that requests to /customers/** and /orders/** will be forwarded to services named customer-service and order-service, respectively. Zuul uses the service registry provided by our Discovery Service to locate each target service.[1]

# Summary

Let’s run through what currently happens in our system:

- **The Discovery Service** must be started first. When it loads it sits idle, waiting for incoming connections.
- Upon starting up, the other services talk to the **Discovery Service** to register themselves and schedule a regular heartbeat to be sent. They also regularly request the registry information from the **Discovery Service**, which responds with the details of all of the registered services.
- When a request is sent to the **Gateway Service**, it checks its mapped routes for a match. If it finds one, it looks up the name of the target service in its local registry that it retrieved from the **Discovery Service**, to work out the physical address of the target service and then proxies the incoming request to it.
- The target service handles the incoming request and responds back to the **Gateway Service** which then responds back to the client.[1]


# References
1. ^Jonathon Henderson,31 October 2019, https://blog.scottlogic.com/2019/10/31/building-microservices-with-spring-boot.html
