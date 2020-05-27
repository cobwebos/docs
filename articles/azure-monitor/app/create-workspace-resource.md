---
title: 新建基于工作区的 Azure Monitor Application Insights 资源 | Microsoft Docs
description: 了解启用基于工作区的新 Azure Monitor Application Insights 资源所需的步骤。
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211403"
---
# <a name="workspace-based-application-insights-resources-preview"></a>基于工作区的 Application Insights 资源（预览版）

基于工作区的资源支持 Application Insights 与 Log Analytics 之间的完全集成。 你现在可以选择将 Application Insights 遥测数据发送到一个公用 Log Analytics 工作区，这样就可以全权访问 Log Analytics 的所有功能，同时将应用程序、基础结构和平台日志置于单一的合并位置中。

这样还可以跨资源实现公用的基于角色的访问控制 (RBAC)，并消除进行跨应用/工作区查询的需要。

> [!NOTE]
> 基于工作区的 Application Insights 资源的数据引入和保留操作通过数据所在的 Log Analytics 工作区计费。 [详细了解]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights)基于工作区的 Application Insights 资源的计费。

若要测试新体验，请登录到 [Azure 门户](https://portal.azure.com)，并创建 Application Insights 资源：

![基于工作区的 Application Insights 资源](./media/create-workspace-resource/create-workspace-based.png)

如果你还没有现有的 Log Analytics 工作区，请[参阅 Log Analytics 工作区创建文档](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

对于公共预览版，**基于工作区的资源当前仅限在“美国西部 2”、“美国东部”和“美国中南部”使用。**

创建资源后，可在“概览”窗格中看到相应的工作区信息：

![工作区名称](./media/create-workspace-resource/workspace-name.png)

单击蓝色链接文本会转到关联的 Log Analytics 工作区，你可以在其中利用新的统一工作区查询环境。

> [!NOTE]
> 我们仍然针对 Application Insights 体验中的 Application Insights 经典资源查询、工作簿和基于日志的警报提供完全的后向兼容性。 若要根据[新的基于工作区的表结构/架构](apm-tables.md)进行查询/查看，必须先导航到 Log Analytics 工作区。 在预览版期间，从 Application Insights 窗格中选择“日志”即可访问经典 Application Insights 查询体验。

## <a name="copy-the-connection-string"></a>复制连接字符串

[连接字符串]()用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

![单击并复制检测密钥](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>监视配置

创建基于工作区的 Application Insights 资源后，配置监视相对简单。

### <a name="code-based-application-monitoring"></a>基于代码的应用程序监视

对于基于代码的应用程序监视，只需安装合适的 Application Insights SDK，并将其指向新建资源的检测密钥或连接字符串。  

若要详细了解如何设置 Application Insights SDK 以进行基于代码的监视，请参阅特定于语言/框架的文档：

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [后台任务和新式控制台应用程序 (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [经典控制台应用程序 (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java ](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>无代码监视和 Visual Studio 资源创建

对于 Azure Functions 和 Azure 应用服务等服务的无代码监视，还需要先创建基于工作区的 Application Insights 资源，然后在监视配置阶段指向该资源。

尽管这些服务提供了在其自己的资源创建过程中创建新 Application Insights 资源的选项，但通过这些 UI 选项创建的资源当前仅限于经典 Application Insights 体验。

这同样适用于 Visual Studio for ASP.NET 和 Visual Studio for ASP.NET Core 中的 Application Insights 资源创建体验。 你必须从 Visual Studio 监视启用 UI 中选择一个基于工作区的现有资源。 选择从 Visual Studio 中创建新资源时，只能创建经典 Application Insights 资源。

## <a name="creating-a-resource-automatically"></a>自动创建资源

### <a name="azure-cli"></a>Azure CLI

若要访问预览版 Application Insights Azure CLI 命令，首先需要运行以下命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，则会看到一条错误消息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下命令来创建 Application Insights 资源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

有关此命令的完整 Azure CLI 文档，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)。

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 命令 `New-AzApplicationInsights` 当前不支持创建基于工作区的 Application Insights 资源。 若要使用 PowerShell 创建基于工作区的资源，可以使用以下 Azure 资源管理器模板，并使用 PowerShell 进行部署。

### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

#### <a name="template-file"></a>模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>参数文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>修改关联的工作区

创建基于工作区的 Application Insights 资源后，可以修改关联的 Log Analytics 工作区。

从“Application Insights 资源”窗格中，选择“属性” > “更改工作区” > “Log Analytics 工作区”  

## <a name="next-steps"></a>后续步骤

* [探索指标](../../azure-monitor/platform/metrics-charts.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
