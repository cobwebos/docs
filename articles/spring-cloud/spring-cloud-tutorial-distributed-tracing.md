---
title: 将分布式跟踪与 Azure Spring Cloud 配合使用
description: 了解如何通过 Azure Application Insights 使用 Spring Cloud 的分布式跟踪
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 97926d5bdf3123ae50714d36ad0234872f67aa96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908287"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>将分布式跟踪与 Azure Spring Cloud 配合使用

使用 Azure Spring Cloud 中的分布式跟踪工具，可以轻松地调试和监视复杂问题。 Azure Spring Cloud 将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 集成。 这种集成可以通过 Azure 门户提供强大的分布式跟踪功能。

::: zone pivot="programming-language-csharp"
本文介绍如何使 .NET Core Steeltoe 应用程序使用分布式跟踪。

## <a name="prerequisites"></a>必备条件

若要执行这些过程，需要一个已 [准备好部署到 Azure 春季云](spring-cloud-tutorial-prepare-app-deployment.md)的 Steeltoe 应用。

## <a name="dependencies"></a>依赖项

安装以下 NuGet 包

* [Steeltoe. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-startupcs"></a>更新 Startup.cs

1. 在 `ConfigureServices` 方法中，调用 `AddDistributedTracing` 和 `AddZipkinExporter` 方法。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

1. 在 `Configure` 方法中调用 `UseTracingExporter` 方法。

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseTracingExporter();
   }
   ```

## <a name="update-configuration"></a>更新配置

将以下设置添加到在 Azure 春季云中运行应用时将使用的配置源：

1. 将 `management.tracing.alwaysSample` 设置为 true。

2. 如果要查看在 Eureka 服务器、配置服务器和用户应用之间发送的跟踪跨越，请将设置 `management.tracing.egressIgnorePattern` 为 "/api/v2/spans |/v2/apps/.*/permissions |/eureka/.*|/oauth/.*".

例如， * 上的appsettings.js* 将包括以下属性：
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

有关 .NET Core Steeltoe apps 中的分布式跟踪的详细信息，请参阅 Steeltoe 文档中的 [分布式跟踪](https://steeltoe.io/docs/3/tracing/distributed-tracing) 。
::: zone-end
::: zone pivot="programming-language-java"
在本文中，学习如何：

> [!div class="checklist"]
> * 在 Azure 门户中启用分布式跟踪。
> * 将 Spring Cloud Sleuth 添加到应用程序。
> * 查看微服务应用程序的依赖项映射。
> * 通过不同的筛选器搜索跟踪数据。

## <a name="prerequisites"></a>先决条件

若要完成这些过程，需要一个已预配且正在运行的 Azure Spring Cloud 服务。 完成 [部署第一个 Azure 春季 cloud 应用程序](spring-cloud-quickstart.md) 快速入门，预配和运行 Azure 春季云服务。

## <a name="add-dependencies"></a>添加依赖项

1. 将以下行添加到 application.properties 文件：

   ```xml
   spring.zipkin.sender.type = web
   ```

   完成此更改后，Zipkin 发送方就可以将内容发送到 Web。

1. 如果已按 [Azure Spring Cloud 应用程序准备指南](spring-cloud-tutorial-prepare-app-deployment.md)进行了操作，则可跳过此步骤。 否则，请转到本地开发环境并编辑 pom.xml 文件，以添加以下 Spring Cloud Sleuth 依赖项：

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. 再次为 Azure Spring Cloud 服务执行生成和部署操作，使之反映这些更改。

## <a name="modify-the-sample-rate"></a>修改采样率

可以通过修改采样率，更改遥测数据收集速率。 例如，如果要按平常一半的频率采样，请打开 application.properties 文件并更改以下行：

```xml
spring.sleuth.sampler.probability=0.5
```

如果已生成并部署应用程序，则可修改采样率。 为此，可在 Azure CLI 或 Azure 门户中添加上一行作为环境变量。
::: zone-end

## <a name="enable-application-insights"></a>启用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”页上，选择“分布式跟踪”。 
1. 选择“编辑设置”，以便编辑现有设置或添加新设置。
1. 新建一个 Application Insights 查询，或选择一个现有的。
1. 选择要监视哪个日志记录类别，并指定保留时间（天）。
1. 选择“应用”以应用新跟踪。

## <a name="view-the-application-map"></a>查看应用程序映射

返回到“分布式跟踪”页面，选择“查看应用程序映射”。  查看应用程序和监视设置的视觉表现形式。 若要了解如何使用应用程序，请参阅[应用程序映射：会审分布式应用程序](https://docs.microsoft.com/azure/azure-monitor/app/app-map)。

## <a name="use-search"></a>使用搜索

使用搜索函数查询其他特定的遥测项。 在“分布式跟踪”页面上，选择“搜索”。  有关如何使用搜索函数的详细信息，请参阅[在 Application Insights 中使用搜索](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)。

## <a name="use-application-insights"></a>使用 Application Insights

除了应用程序映射和搜索功能，Application Insights 还提供监视功能。 在 Azure 门户中搜索应用程序的名称，然后打开 Application Insights 页面来查找监视信息。 有关如何使用这些工具的更多指南，请查看 [Azure Monitor 日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

## <a name="disable-application-insights"></a>禁用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”部分，选择“分布式跟踪”。 
1. 选择“禁用”，禁用 Application Insights。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Spring Cloud 中启用并了解分布式跟踪。 若要了解如何将服务绑定到应用程序，请参阅[将 Azure Cosmos DB 数据库绑定到 Azure Spring Cloud 应用程序](spring-cloud-tutorial-bind-cosmos.md)。
