# Inter-Service Communication

Contrary to a more traditional, monolithic approach where a single database is usually available to all parts of an application, microservices run as distinct processes with their own private data stores and must communicate with one another to achieve their goals. This fundamental difference requires a different mindset and can be one of the biggest challenges facing developers when transitioning to building microservices.[1]

There are many different approaches to inter-service communication, each suited for different scenarios. In this tutorial however, we’ll focus on synchronous HTTP.[1]

# HTTP Clients with Feign
Making HTTP requests in Java and Spring isn’t too difficult. Java 9 introduced an incubating HttpClient, which was standardised in Java 11 and there are also a number of well known, open-source HTTP clients available, such as OkHttp, Apache HttpClient and Spring’s RestTemplate.[1]

We could feasibly choose any of these options to manage communication between our services, but there are other problems to solve before we’d be able to do so. Since service addresses are dynamic and unknown ahead of time, we’d need to integrate with the local service registry to look them up before making requests. We’d also need to invest additional time implementing functions like (de)serialization, fault detection and load distribution.[1]

**Feign** is a project, originally sponsored by Netflix which was designed to allow developers to declaratively build HTTP clients by means of creating annotated interfaces. It is highly customisable, and supports a significant number of integrations out of the box. Spring Cloud OpenFeign adds support for Feign by configuring and binding it to the Spring environment, integrating Spring MVC style annotations for building HTTP clients, and connecting it to the rest of the Spring Cloud ecosystem.


# Client-Side Load Balancing with Ribbon
**Ribbon** is an Inter Process Communication library written by Netflix. Its primary purpose is to provide client-side load balancing but also integrates nicely with other cloud services such as Eureka for service discovery and Hystrix to provide resilience.[1]*( **Ribbon** Netflix tarafından yazılan İşlemler Arası İletişim kütüphanesidir. Birincil amacı, istemci tarafı yük dengeleme sağlamaktır, ancak aynı zamanda servis keşfi için Eureka ve esneklik sağlamak için Hystrix gibi diğer bulut hizmetleriyle de bütünleşir.)*

We’re already using Ribbon for client-side load balancing in our project since Spring Cloud OpenFeign (at the time of writing) uses Ribbon as its default load balancer.[1].*(Spring Cloud OpenFeign (yazma sırasında) varsayılan yük dengeleyici olarak Ribbon kullandığından, projemizde istemci tarafı yük dengelemesi için Ribbon'u zaten kullanıyoruz.)*

Boot the Discovery, Customer and Gateway services as normal. Then we’ll launch multiple instances of the Order Service on different ports using the following command (replace <port> with a different number each time):[1]

```
./gradlew bootRun --args='--server.port=<port>'
```
Wait a couple of minutes for everything to register with the Discovery Service and for the updated registries to be propagated. Take a look at the Eureka dashboard - under Instances currently registered with Eureka you should see multiple instances of the Order Service:

![Alt text](images/instances-registered-with-eureka1.jpg?raw=true "Title")


Make a few GET requests to http://localhost/customers/2/orders and take note of the port in the response. It should change each time.

The Feign client uses Ribbon to balance requests between instances of our Order Service. Ribbon uses the local service registry, to find all of the physical addresses of the Order Service, and uses a pluggable load-balancing algorithm to determine which instance should receive the next request.[1]

By default, our Gateway Service, set up as a Zuul proxy, also uses Ribbon to balance requests between our services. Make a GET request to http://localhost/orders, taking note of the port again.























# References
1. ^Jonathon Henderson, 19 November 2019, https://blog.scottlogic.com/2019/11/19/building-microservices-with-spring-boot-2.html
