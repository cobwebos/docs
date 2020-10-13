---
title: 将 Azure Monitor Application Insights 经典资源迁移到基于工作区的资源 |Microsoft Docs
description: 了解将 Azure Monitor Application Insights 经典资源升级到新的基于工作区的模型所需的步骤。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 0d2c7d1b9ee57e6d201205c04557e1b5f5623eb0
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930571"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>迁移到基于工作区的 Application Insights 资源

本指南将指导你完成将经典 Application Insights 资源迁移到基于工作区的资源的过程。 基于工作区的资源支持 Application Insights 与 Log Analytics 之间的完全集成。 基于工作区的资源将 Application Insights 遥测发送到公共 Log Analytics 工作区，该工作区允许您访问 [Azure Monitor 的最新功能](#new-capabilities) ，同时将应用程序、基础结构和平台日志保持在单个合并位置。

基于工作区的资源可在资源中 (RBAC) Role-Based 常见的访问控制，并消除了跨应用/工作区查询的需要。

**基于工作区的资源当前在所有商业区域和 Azure 美国政府版中可用**

## <a name="new-capabilities"></a>新功能

基于工作区的 Application Insights 允许您充分利用 Azure Monitor 和 Log Analytics 的所有最新功能，包括：

* [ (CMK 的客户托管密钥) ](../platform/customer-managed-keys.md) 使用只有你有权访问的加密密钥为你的数据提供静态加密。
* 通过 [Azure 专用链接](../platform/private-link-security.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。
* [为探查器和 Snapshot Debugger 提供自己的存储 (BYOS) ](./profiler-bring-your-own-storage.md) ，你可以完全控制静态加密策略、生存期管理策略以及与 Application Insights Profiler 和 Snapshot Debugger 相关的所有数据的网络访问。 
* 与即用即付价格相比，[容量预留层](../platform/manage-cost-storage.md#pricing-model)最多可节省25%。 
* 通过 Log Analytics 流引入提高数据引入速度。

## <a name="migration-process"></a>迁移过程

迁移到基于工作区的资源时，不会将任何数据从经典资源的存储传输到新的基于工作区的存储。 选择迁移将改为更改新数据写入到 Log Analytics 工作区的位置，同时保留对经典资源数据的访问。 

经典资源数据将保持不变，并受经典 Application Insights 资源上的保留设置的限制。 迁移后的所有新数据引入将受到关联 Log Analytics 工作区的 [保留设置](../platform/manage-cost-storage.md#change-the-data-retention-period) 的限制，这些设置还支持 [按数据类型进行不同的保留设置](../platform/manage-cost-storage.md#retention-by-data-type)。
迁移过程是 **永久性的，无法撤消**。 将资源迁移到基于工作区的 Application Insights 后，它将始终是基于工作区的资源。 但是，一旦迁移，就可以根据需要随时更改目标工作区。 

> [!NOTE]
> 基于工作区的 Application Insights 资源的数据引入和保留 [通过数据所在 Log Analytics 工作区进行计费](../platform/manage-cost-storage.md) 。 如果在迁移之前选择了 "数据引入 Application Insights" 的数据保留期超过90天，则在迁移之前，数据保留期将继续按 Application Insights 资源计费。 [详细了解]( ./pricing.md#workspace-based-application-insights)基于工作区的 Application Insights 资源的计费。

如果不需要迁移现有资源，而是想要创建新的基于工作区的 Application Insights 资源，请使用 [基于工作区的资源创建指南](create-workspace-resource.md)。

## <a name="pre-requisites"></a>先决条件 

- 一个 Log Analytics 工作区，其访问控制模式设置为设置 **`use resource or workspace permissions`** 。 

    - 基于工作区的 Application Insights 资源与设置为专用设置的工作区不兼容 **`workspace based permissions`** 。 若要详细了解 Log Analytics 工作区访问控制，请参阅 [Log Analytics 配置访问控制模式指南](../platform/manage-access.md#configure-access-control-mode)

    - 如果你还没有现有的 Log Analytics 工作区，请[参阅 Log Analytics 工作区创建文档](../learn/quick-create-workspace.md)。
    
- 基于工作区的资源不支持连续导出，因此必须禁用。
迁移完成后，可以使用 [诊断设置](../platform/diagnostic-settings.md) 配置到存储帐户的数据存档或向 Azure 事件中心进行流式传输。  

- 检查**General**  >  Log Analytics 工作区的 "常规**使用情况和估计成本**"  >  **数据保留**期。 此设置将影响迁移 Application Insights 资源后，存储任何新的引入数据的时间。 如果你当前将 Application Insights 的数据存储的时间超过默认的90天，并且想要保留此更长的保留期，则可能需要调整你的工作区保持期设置。

## <a name="migrate-your-resource"></a>迁移资源

本部分将指导你完成将经典 Application Insights 资源迁移到基于工作区的新资源类型的过程。

1. 在 Application Insights 资源的左侧菜单栏中，选择 "**配置**" 标题下的 "**属性**"。

    ![以红色框突出显示的属性](./media/convert-classic-resource/properties.png)

2. 选择 `Migrate to Workspace-based`。
    
     !["迁移资源" 按钮](./media/convert-classic-resource/migrate.png)

3. 选择要在其中存储所有未来引入 Application Insights 遥测的 Log Analytics 工作区。

     ![迁移向导 UI，具有选择目标工作区的选项](./media/convert-classic-resource/migration.png)
    

迁移资源后，你将在 " **概述** " 窗格中看到相应的工作区信息：

![工作区名称](./media/create-workspace-resource/workspace-name.png)

单击蓝色链接文本会转到关联的 Log Analytics 工作区，你可以在其中利用新的统一工作区查询环境。

## <a name="understanding-log-queries"></a>了解日志查询

我们仍然针对 Application Insights 体验中的 Application Insights 经典资源查询、工作簿和基于日志的警报提供完全的后向兼容性。 

若要针对 [基于工作区的新表结构/架构](apm-tables.md)编写查询，必须先导航到 Log Analytics 工作区。 

当你从工作区中的 Log Analytics UI 直接查询时，你只会看到迁移后的引入数据。 若要在统一查询体验中的迁移后查看经典 Application Insights 数据 + 新数据引入，请在迁移的 Application Insights 资源中使用日志 (Analytics) 查询视图。

## <a name="programmatic-resource-migration"></a>编程资源迁移

### <a name="azure-cli"></a>Azure CLI

若要访问预览版 Application Insights Azure CLI 命令，首先需要运行以下命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，则会看到一条错误消息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下命令来创建 Application Insights 资源：

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

有关此命令的完整 Azure CLI 文档，请参阅 [Azure CLI 文档](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-update)。

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`PowerShell 命令当前不支持将经典 Application Insights 资源迁移到基于工作区。 若要使用 PowerShell 创建基于工作区的资源，可以使用以下 Azure 资源管理器模板，并使用 PowerShell 进行部署。

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

从 "Application Insights 资源" 窗格中，选择 "**属性**" "  >  **更改工作区**"  >  **Log Analytics 工作**区 "。

## <a name="troubleshooting"></a>疑难解答

### <a name="access-mode"></a>访问模式

**错误消息：** *所选工作区配置了基于工作区的访问模式。某些 APM 功能可能会受到影响。在工作区设置中选择另一个工作区或允许基于资源的访问权限。可以使用 CLI 覆盖此错误。* 

要使基于工作区的 Application Insights 资源正常运行，需要将目标 Log Analytics 工作区的访问控制模式改为 **资源或工作区权限** 设置。 此设置位于 "**属性**" "  >  **访问控制" 模式**下的 Log Analytics 工作区 UI 中。 有关详细说明，请参阅 [Log Analytics 配置访问控制模式指南](../platform/manage-access.md#configure-access-control-mode)。 如果访问控制模式设置为 "独占 **要求工作区权限** " 设置，则通过门户迁移体验进行的迁移将保持阻止状态。

如果由于当前目标工作区的安全原因无法更改访问控制模式，我们建议创建新的 Log Analytics 工作区以用于迁移。 

### <a name="continuous-export"></a>连续导出

**错误消息：** *需要先禁用连续导出，然后才能继续。迁移后，请使用诊断设置进行导出。* 

基于工作区的资源不支持旧版连续导出功能。 在迁移之前，需要禁用连续导出。

1. 从 Application Insights 资源视图的 " **配置** " 标题下，选择 " **连续导出**"。

    ![连续导出菜单项](./media/convert-classic-resource/continuous-export.png)

2. 选择“禁用”。****

    ![连续导出禁用按钮](./media/convert-classic-resource/disable.png)

- 选择 "禁用" 后，可以导航回迁移 UI。 如果 "编辑连续导出" 页提示您的设置不会保存，您可以在此提示中选择 "确定"，因为它与禁用/启用连续导出无关。

- 将 Application Insights 资源成功迁移到基于工作区的后，可以使用诊断设置来替换用于提供的连续导出功能。 选择 "**诊断设置**"  >  ，从 Application Insights 资源中添加 "**诊断设置**"。 可以选择所有表，或是要存档到存储帐户或流式传输到 Azure 事件中心的表的子集。 有关诊断设置的详细指南，请参阅 [Azure Monitor 诊断设置指南](../platform/diagnostic-settings.md)。

### <a name="retention-settings"></a>保留设置

**警告消息：** *自定义的 Application Insights 保留设置不适用于发送到工作区的数据。需要单独重新配置此配置。*

在迁移之前无需进行任何更改，但此消息会提醒你当前 Application Insights 保留期设置未设置为默认的90天保留期。 此警告消息表示，你可能需要在迁移和开始引入新数据之前，修改 Log Analytics 工作区的保留设置。 

你可以在 "**常规**  >  **使用情况" 和**  >  "从 Log Analytics UI 中估计的**数据保留**成本" 下查看当前 Log Analytics 的保留设置。 此设置将影响迁移 Application Insights 资源后，存储任何新的引入数据的时间。

## <a name="next-steps"></a>后续步骤

* [探索指标](../platform/metrics-charts.md)
* [编写分析查询](../log-query/log-query-overview.md)
