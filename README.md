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















# References
1. ^Jonathon Henderson,31 October 2019, https://blog.scottlogic.com/2019/10/31/building-microservices-with-spring-boot.html
