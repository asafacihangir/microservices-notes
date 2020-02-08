# Building Microservices
The term “microservices” is used to describe a software architectural design whereby many loosely-coupled components run independently, but ultimately work together as a single application. Services typically focus on particular aspects of a business domain or business entities and they tyically use a network to communicate.[1] *(“Mikroservisler” terimi, loosely-coupled  birçok bileşenin bağımsız olarak çalıştığı, ancak sonuçta tek bir uygulama olarak birlikte çalıştığı bir yazılım mimari tasarımını tanımlamak için kullanılır. Servisler genellikle business domain veya business entity'lerin belirli yönlerine odaklanır ve tipik olarak aralarında iletişim kurmak için bir network kullanırlar.)*

Netflix became one of the earliest adopters of microservices, having transitioned their monolithic application to a horizontally scalable, distributed architecture long before the term “microservices” gained any sort of traction.[1] *(Netflix, “mikroservis” terimi herhangi bir popülarite kazanmadan çok önce, monolitik uygulamalarını yatay olarak ölçeklenebilir, dağıtılmış bir mimariye geçirmiş olan mikroservislerin ilk uygulayıcılarından biri oldu.)*

Over the years Netflix have open-sourced a number of tools (that they continue to use internally) as part of the Netflix Open Source Software Center (Netflix OSS) project. Many of these tools have been adopted by the Spring team as part of the Spring Cloud project, which provides tools to assist developers with some of the common patterns used when building distributed systems.[1]. *(Netflix, yıllar içinde (Netflix OSS) projesinin bir parçası olarak bazı araçlarını(dahili olarak kullanmaya devam ettikleri) açık kaynaklı hale getirmiştir. Bu araçların birçoğu, Spring ekibi tarafından; geliştiricilere, dağıtılmış sistemler oluştururken kullanılan bazı yaygın kalıplar konusunda yardımcı olacak araçlar sağlayan Spring Cloud projesinin bir parçası olarak benimsenmiştir.)*


# The Project
Let’s imagine a simple online store scenario, where customers can place orders. We can already identify some services that we’ll need - a customer service and an order service. We’ll take it step by step to build out each one.[1]






# References
1. ^Jonathon Henderson,31 October 2019, https://blog.scottlogic.com/2019/10/31/building-microservices-with-spring-boot.html
