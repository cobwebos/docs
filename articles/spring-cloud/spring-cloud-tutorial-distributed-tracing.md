---
title: 教程 - 将分布式跟踪与 Azure Spring Cloud 配合使用
description: 本教程介绍如何通过 Azure Application Insights 使用 Spring Cloud 的分布式跟踪
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708757"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>教程：将分布式跟踪与 Azure Spring Cloud 配合使用

Spring Cloud 的分布式跟踪工具可以方便地用来对复杂的问题进行调试和监视。 Azure Spring Cloud 将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 集成，可以通过 Azure 门户提供强大的分布式跟踪功能。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中启用分布式跟踪
> * 将 Spring Cloud Sleuth 添加到应用程序
> * 查看微服务应用程序的依赖项映射
> * 通过不同的筛选器搜索跟踪数据

## <a name="prerequisites"></a>先决条件

完成本教程：

* 一项已预配的处于运行状态的 Azure Spring Cloud 服务。  完成[此快速入门](spring-cloud-quickstart-launch-app-cli.md)，了解如何预配并启动 Azure Spring Cloud 服务。
    
## <a name="add-dependencies"></a>添加依赖项

允许 zipkin 发送方将内容发送到 Web，方法是将以下行添加到 application.properties 文件：

```xml
spring.zipkin.sender.type = web
```

如果已按 [Azure Spring Cloud 应用程序准备指南](spring-cloud-tutorial-prepare-app-deployment.md)进行了操作，则可跳过下一步。 否则，请转到本地开发环境并编辑 `pom.xml` 文件，使之包含 Spring Cloud Sleuth 依赖项：

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

* 再次为 Azure Spring Cloud 服务执行生成和部署操作，使之反映这些更改。 

## <a name="modify-the-sample-rate"></a>修改采样率
可以通过修改采样率，更改遥测数据收集速率。 例如，如果要按平常一半的频率采样，请转到 `application.properties` 文件并更改以下行：

```xml
spring.sleuth.sampler.probability=0.5
```

在已生成并部署应用程序的情况下，若要修改采样率，则可在Azure CLI 或门户中添加以上行作为环境变量。 

## <a name="enable-application-insights"></a>启用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”部分，选择“分布式跟踪”。 
1. 选择“编辑设置”，以便编辑现有设置或添加新设置  。
1. 新建一个 Application Insights 查询，也可选择一个现有的查询。
1. 选择要监视哪个日志记录类别，并指定保留时间（天）。
1. 选择“应用”以应用新跟踪。 

## <a name="view-application-map"></a>查看应用程序映射

返回到“分布式跟踪”页面，选择“查看应用程序映射”。  查看应用程序和监视设置的视觉表现形式。 若要了解如何使用应用程序，请参阅[此文](https://docs.microsoft.com/azure/azure-monitor/app/app-map)。

## <a name="search"></a>搜索

使用搜索函数查询其他特定的遥测项。 在“分布式跟踪”页面上，选择“搜索”。   有关如何使用搜索函数的详细信息，请参阅[此文](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)。

## <a name="application-insights-page"></a>Application Insights 页面

除了应用程序映射和搜索，Application Insights 还提供监视功能。 在 Azure 门户中搜索应用程序的名称，然后启动 Application Insights 页面来查找更多内容。 有关如何使用这些工具的更多指南，请[查看文档](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。


## <a name="disable-application-insights"></a>禁用 Application Insights

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。
1. 在“监视”部分，单击“分布式跟踪”。 
1. 单击“禁用”，禁用 Application Insights 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在 Azure Spring Cloud 中启用并了解分布式跟踪。 若要了解如何将应用程序绑定到 Azure CosmosDB，请继续学习下一教程。

> [!div class="nextstepaction"]
> [了解如何将应用程序绑定到 Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md)。
