---
title: 如何将 Micrometer 与 Azure Application Insights Java SDK 配合使用 | Microsoft Docs
description: '有关对 Application Insights Spring Boot 和非 Spring Boot 应用程序使用 Micrometer 的分步指导。 '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 25ee061f489df0a666d59871661478f987b28e3f
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344801"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>如何将 Micrometer 与 Azure Application Insights Java SDK 配合使用
Micrometer 应用程序监视功能可以度量基于 JVM 的应用程序代码的指标，并可用于将数据导出到偏爱的监视系统。 本文介绍如何对 Spring Boot 和非 Spring Boot 应用程序配合使用 Micrometer 与 Application Insights。

## <a name="using-spring-boot-15x"></a>使用 Spring Boot 1.5x
将以下依赖项添加到 pom.xml 或 build.gradle 文件： 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA 或更高版本
* Micrometer Azure Registry 1.1.0 或更高版本
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 或更高版本（用于向后移植 Spring 框架中的自动配置代码）。
* [ApplicationInsights 资源](app-insights-create-new-resource.md)

Steps

1. 更新 Spring Boot 应用程序的 pom.xml 文件，在其中添加以下依赖项：

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. 使用以下属性更新 application.properties 或 yml 文件中的 Application Insights 检测密钥：

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. 生成并运行应用程序
2. 上述代码应会使用自动收集到 Azure Monitor 的预先聚合指标启动并运行应用程序。 有关如何微调 Application Insights Spring Boot Starter 的详细信息，请参阅 [GitHub 上的自述文件](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)。

## <a name="using-spring-2x"></a>使用 Spring 2.x

将以下依赖项添加到 pom.xml 或 build.gradle 文件：

* Application Insights Spring-boot-starter 2.1.2 或更高版本
* Azure-spring-boot-metrics-starters 2.0.7 或更高版本  
* [Application Insights 资源](app-insights-create-new-resource.md)

步骤：

1. 更新 Spring Boot 应用程序的 pom.xml 文件，在其中添加以下依赖项：

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. 使用以下属性更新 application.properties 或 yml 文件中的 Application Insights 检测密钥：

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. 生成并运行应用程序
4. 上述代码应会使用自动收集到 Azure Monitor 的预先聚合指标运行应用程序。 有关如何微调 Application Insights Spring Boot Starter 的详细信息，请参阅 [GitHub 上的自述文件](https://github.com/Microsoft/azure-spring-boot/releases/latest)。

默认指标：

*    为 Tomcat、JVM 自动配置的指标、Logback 指标、Log4J 指标、运行时间指标、处理器指标、FileDescriptorMetrics。
*    例如，如果类路径中存在 netflix hystrix，则我们也可以获取这些指标。 
*    可通过添加相应的 bean 来获取以下指标。 
        - CacheMetrics（CaffineCache、EhCache2、GuavaCache、HazelcaseCache、Jcache）     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 指标 
        - Kafka 指标 

 

如何禁用自动指标收集： 
 
- JVM 指标： 
    - management.metrics.binders.jvm.enabled=false 
- Logback 指标： 
    - management.metrics.binders.logback.enabled=false
- 运行时间指标： 
    - management.metrics.binders.uptime.enabled=false 
- 处理器指标：
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics：
    - management.metrics.binders.files.enabled=false 
- Hystrix 指标（如果类路径中包含该库）： 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ 指标（如果类路径中包含该库）： 
    - spring.aop.enabled=false 

> [!NOTE]
> 在 Spring Boot 应用程序的 application.properties 或 application.yml 文件中指定上述属性

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>对非 Spring Boot Web 应用程序使用 Micrometer

将以下依赖项添加到 pom.xml 或 build.gradle 文件：
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) 或更高版本
* [Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) 或更高版本
* [注册 Web 筛选器](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer Azure Registry 1.1.0 或更高版本
* [Application Insights 资源](app-insights-create-new-resource.md)

步骤：

1. 将以下依赖项添加到 pom.xml 或 build.gradle 文件中：

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. 将 Application Insights.xml 放入 resources 文件夹

    示例 Servlet 类（发出计时器指标）：

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

      示例配置类：

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

若要详细了解指标，请参阅 [Micrometer 文档](https://micrometer.io/docs/)。

有关演示如何创建不同类型的指标的其他示例代码，请参阅[官方的 Micrometer Github 存储库](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)。

## <a name="how-to-bind-additional-metrics-collection"></a>如何绑定其他指标集合

### <a name="springbootspring"></a>SpringBoot/Spring

创建相应指标类别的 bean。 例如，假设我们需要 Guava 缓存指标：

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
有多个指标默认未启用，但可按上述方式绑定。 有关完整列表，请参阅[官方的 Micrometer Github 存储库](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )。

### <a name="non-spring-apps"></a>非 Spring 应用
将以下绑定代码添加到配置文件：
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Micrometer，请参阅官方的 [Micrometer 文档](https://micrometer.io/docs)。
* 若要了解 Azure 上的 Spring，请参阅官方的[“Azure 上的 Spring”文档](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)。
