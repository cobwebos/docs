---
title: "Azure Application Insights 对多个角色、微服务和容器的支持 | Microsoft Docs"
description: "监视由多个组件或角色组成的应用的性能和使用情况。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>使用 Application Insights（预览）监视多角色应用程序

可以使用 [Azure Application Insights](app-insights-overview.md) 监视由多个组件、角色或服务组成的应用程序。 角色的运行状况及其之间的关系显示在单个应用程序映射中。 例如，可以使用自动 HTTP 关联通过多个角色跟踪各项操作。 容器诊断可与应用程序遥测集成和关联。 对应用程序的所有角色使用单个 Application Insights 资源。 

![多角色应用程序映射](./media/app-insights-monitor-multi-role-apps/app-map.png)

本文使用“角色”来泛指以独立项目形式构建的任何应用程序组件或服务。 例如，典型的商业应用程序可能由多个通过 REST API 通信的角色组成。 角色可以托管在 Docker 或 Service Fabric 等容器中，也可以托管在云或本地主机中。 

### <a name="sharing-a-single-application-insights-resource"></a>共享单个 Application Insights 资源 

此处的关键技术是将遥测数据从应用程序中的每个角色发送到同一个 Application Insights 资源，但必要时使用 `cloud_RoleName` 属性来区分角色。 

在某些情况下，这种方法可能不适用，你可能偏向于对不同的角色组使用不同的资源。 例如，可能需要为管理或计费目的使用不同的资源。 使用不同的资源意味着单个应用程序映射中不会显示所有角色，并且无法在 [Analytics](app-insights-analytics.md) 中跨角色查询。 此外，必须设置不同的资源。

考虑到这一点，我们假设在本文档的余下部分中，你要将数据从多个角色发送到一个 Application Insights 资源。

## <a name="configure-multi-role-applications"></a>配置多角色应用程序

若要获取多角色应用程序映射，需要实现以下目标：

* 在应用程序的每个角色中**安装最新预发行** Application Insights 包。 
* 对应用程序的所有角色**共享单个 Application Insights 资源**。
* 在“预览”边栏选项卡中**启用多角色应用程序映射**。

根据应用程序的类型使用相应的方法配置应用程序的每个角色。 （[ASP.NET](app-insights-asp-net.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md)。）

### <a name="1-install-the-latest-pre-release-package"></a>1.安装最新的预发行包

在项目中为每个角色更新或安装 Appication Insights 包。 如果使用 Visual Studio：

1. 右键单击某个项目，然后选择“管理 NuGet 包”。 
2. 选择“包括预发行版”。
3. 如果 Application Insights 包显示在“更新”中，请选择这些包。 

    否则，请浏览并安装相应的包：
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - 适用于以来宾可执行文件形式运行的角色，以及 Service Fabric 应用程序中运行的 Docker 容器
    * Microsoft.ApplicationInsights.ServiceFabric.Native - 适用于 ServiceFabric 应用程序中的 Reliable Services
    * Microsoft.ApplicationInsights.Kubernetes - 适用于 Docker on Kubernetes 中运行的角色

### <a name="2-share-a-single-application-insights-resource"></a>2.共享单个 Application Insights 资源

* 在 Visual Studio 中右键单击某个项目，然后选择“配置 Application Insights”或“Application Insights”>“配置”。 对于第一个项目，请使用向导创建 Application Insights 资源。 对于后续项目，请选择相同的资源。
* 如果未显示 Application Insights 菜单，请手动配置：

   1. 在 [Azure 门户](https://portal,azure.com)中，打开已经为另一个角色创建的 Application Insights 资源。
   2. 单击“概述”边栏选项卡中打开“概要”下拉选项卡，然后复制**检测密钥**。
   3. 在项目中打开 ApplicationInsights.config 并插入：`<InstrumentationKey>your copied key</InstrumentationKey>`

![将检测密钥复制到 .config 文件](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3.启用多角色应用程序映射

在 Azure 门户中打开应用程序的资源。 在“预览”边栏选项卡中启用“多角色应用程序映射”。

### <a name="4-enable-docker-metrics-optional"></a>4.启用 Docker 指标（可选） 

如果角色在托管于 Azure Windows VM 上的 Docker 中运行，你可以从容器收集其他指标。 请在 [Azure 诊断](../monitoring-and-diagnostics/azure-diagnostics.md)配置文件中插入以下代码：

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>使用 cloud_RoleName 来区分角色

`cloud_RoleName` 属性将附加到所有遥测数据。 它标识遥测数据的来源角色或服务。 （它与 cloud_RoleInstance 不同，后者区分在多个服务器进程或计算机上并行运行的相同角色。）

在门户中，可以使用此属性对遥测数据进行筛选或分段。 在本示例中，“故障”边栏选项卡经过筛选，只显示来自前端 Web 服务的信息，并筛选掉了 CRM API 后端的故障：

![按云角色名称分段的指标图表](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>跟踪角色之间的操作

可以在处理单个操作时，跟踪在不同服务之间发出的调用。


![显示操作的遥测数据](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

不断地单击，直到显示此操作在前端 Web 服务器和后端 API 中的遥测数据的关联列表：

![跨角色搜索](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>后续步骤

* [区分开发、测试和生产环境中的遥测数据](app-insights-separate-resources.md)

