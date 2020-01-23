---
title: 教程 - 将分布式跟踪与 Azure Spring Cloud 配合使用
description: 本教程介绍如何通过 Azure Application Insights 使用 Spring Cloud 的分布式跟踪
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: e4d3a7fbdb938071b754a16179bcd021985383cc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277487"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>将分布式跟踪与 Azure Spring Cloud 配合使用

使用 Azure Spring Cloud 中的分布式跟踪工具，可以轻松地调试和监视复杂问题。 Azure Spring Cloud 将 [Azure Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 集成。 这种集成可以通过 Azure 门户提供强大的分布式跟踪功能。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中启用分布式跟踪。
> * 将 Azure Spring Cloud Sleuth 添加到应用程序。
> * 查看微服务应用程序的依赖项映射。
> * 通过不同的筛选器搜索跟踪数据。

## <a name="prerequisites"></a>必备条件

若要完成本教程，需要一个已预配并正在运行的 Azure Spring Cloud 服务。 完成[有关如何通过 Azure CLI 来部署应用的快速入门](spring-cloud-quickstart-launch-app-cli.md)，了解如何预配并运行 Azure Spring Cloud 服务。
    
## <a name="add-dependencies"></a>添加依赖项

1. 将以下行添加到 application.properties 文件：

   ```xml
   spring.zipkin.sender.type = web
   ```

   完成此更改后，Zipkin 发送方就可以将内容发送到 Web。

1. 如果已按 [Azure Spring Cloud 应用程序准备指南](spring-cloud-tutorial-prepare-app-deployment.md)进行了操作，则可跳过此步骤。 否则，请转到本地开发环境并编辑 pom.xml 文件，使之包含以下 Azure Spring Cloud Sleuth 依赖项：

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

## <a name="enable-application-insights"></a>启用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”页上，选择“分布式跟踪”。  
1. 选择“编辑设置”，以便编辑现有设置或添加新设置  。
1. 新建一个 Application Insights 查询，或选择一个现有的。
1. 选择要监视哪个日志记录类别，并指定保留时间（天）。
1. 选择“应用”以应用新跟踪。 

## <a name="view-the-application-map"></a>查看应用程序映射

返回到“分布式跟踪”页面，选择“查看应用程序映射”。   查看应用程序和监视设置的视觉表现形式。 若要了解如何使用应用程序，请参阅[应用程序映射：会审分布式应用程序](https://docs.microsoft.com/azure/azure-monitor/app/app-map)。

## <a name="use-search"></a>使用搜索

使用搜索函数查询其他特定的遥测项。 在“分布式跟踪”页面上，选择“搜索”。   有关如何使用搜索函数的详细信息，请参阅[在 Application Insights 中使用搜索](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)。

## <a name="use-application-insights"></a>使用 Application Insights

除了应用程序映射和搜索功能，Application Insights 还提供监视功能。 在 Azure 门户中搜索应用程序的名称，然后打开 Application Insights 页面来查找监视信息。 有关如何使用这些工具的更多指南，请查看 [Azure Monitor 日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

## <a name="disable-application-insights"></a>禁用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”部分，选择“分布式跟踪”。  
1. 选择“禁用”，禁用 Application Insights  。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在 Azure Spring Cloud 中启用并了解分布式跟踪。 若要了解如何将应用程序绑定到 Azure Cosmos DB 数据库，请继续学习下一教程。

> [!div class="nextstepaction"]
> [了解如何绑定到 Azure Cosmos DB 数据库](spring-cloud-tutorial-bind-cosmos.md)
