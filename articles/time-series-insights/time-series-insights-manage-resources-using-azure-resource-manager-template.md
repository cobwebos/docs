---
title: 如何使用 Azure 资源管理器模板管理 Azure 时序见解环境 | Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器以编程方式管理 Azure 时序见解环境。
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: 8355248f28a019ef4712f542c8eac731362330ce
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建时序见解资源

本文介绍如何使用 Azure 资源管理器模板、PowerShell 和时序见解资源提供程序创建并部署时序见解资源。

时序见解支持以下资源：
   | 资源 | 说明 |
   | --- | --- |
   | 环境 | 时序见解环境是从事件中转站读取的、经存储的并可供查询使用的事件的逻辑分组。 有关详细信息，请参阅[规划 Azure 时序见解环境](time-series-insights-environment-planning.md) |
   | 事件源 | 事件源是与事件中转站建立的连接，时序见解从该中转站读取和引入事件至环境。 目前支持的事件源是 IoT 中心和事件中心。 |
   | 引用数据集 | 引用数据集提供有关环境中事件的元数据。 在引入期间，引用数据集中的元数据将与事件联接。 引用数据集根据其事件键属性定义为资源。 构成引用数据集的实际元数据通过数据平面 API 上传或修改。 |
   | 访问策略 | 访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。 有关详细信息，请参阅[使用 Azure 门户授予对时序见解环境的数据访问权限](time-series-insights-data-access.md) |

资源管理器模板是用于定义资源组中资源的基础结构和配置的 JSON 文件。 有关详细信息，请参阅以下文档：

- [Azure 资源管理器概述 - 模板部署](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)

GitHub 上已发布 [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) 快速入门模板。 此模板创建一个时序见解环境、一个配置为使用事件中心的事件的子事件源，以及授予环境数据访问权限的访问策略。 如果未指定现有的事件中心，则会连同部署创建一个事件中心。

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>使用 PowerShell 在本地部署快速入门模板

以下过程描述如何使用 PowerShell 部署一个 Azure 资源管理器模板，该模板创建一个时序见解环境、一个配置为使用事件中心的事件的子事件源，以及授予环境数据访问权限的访问策略。 如果未指定现有的事件中心，则会连同部署创建一个事件中心。

大概的工作流如下所示：

1. 安装 PowerShell。
1. 创建模板和参数文件。
1. 在 PowerShell 中，登录到 Azure 帐户。
1. 创建新资源组（如果不存在）。
1. 测试部署。
1. 部署模板。

### <a name="install-powershell"></a>安装 PowerShell

按照 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)中的说明安装 Azure PowerShell。

### <a name="create-a-template"></a>创建模板

从 GitHub 克隆或复制 [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) 模板。

### <a name="create-a-parameters-file"></a>创建参数文件

若要创建参数文件，请复制 [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) 文件。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>必需的参数

   | 参数 | 说明 |
   | --- | --- |
   | eventHubNamespaceName | 源事件中心的命名空间。 |
   | eventHubName | 源事件中心的名称。 |
   | consumerGroupName | 由时序见解服务用来从事件中心读取数据的使用者组的名称。 **注意：**为了避免资源争用，此使用者组必须专门用于时序见解服务，而不能与其他读取者共享。 |
   | environmentName | 环境的名称。 此名称不能包含：'<', '>', '%', '&', ':', '\\', '?', '/' 和任何控制字符。 允许其他所有字符。|
   | eventSourceName | 事件源子资源的名称。 此名称不能包含：'<', '>', '%', '&', ':', '\\', '?', '/' 和任何控制字符。 允许其他所有字符。 |

#### <a name="optional-parameters"></a>可选参数

   | 参数 | 说明 |
   | --- | --- |
   | existingEventHubResourceId | 要通过事件源连接到时序见解环境的现有事件中心的可选资源 ID。 **注意：**部署模板的用户必须有权对事件中心执行 listkeys 操作。 如果未传递任何值，则模板会创建新的事件中心。 |
   | environmentDisplayName | 要在工具或用户界面中显示的可选友好名称，而不是环境名称。 |
   | environmentSkuName | SKU 的名称。 有关详细信息，请参阅[时序见解定价页](https://azure.microsoft.com/pricing/details/time-series-insights/)。  |
   | environmentSkuCapacity | SKU 的单位容量。 有关详细信息，请参阅[时序见解定价页](https://azure.microsoft.com/pricing/details/time-series-insights/)。|
   | environmentDataRetentionTime | 环境事件可供查询的最小时间跨度。 必须以 ISO 8601 格式指定该值，例如“P30D”表示保留策略为 30 天。 |
   | eventSourceDisplayName | 要在工具或用户界面中显示的可选友好名称，而不是事件源名称。 |
   | eventSourceTimestampPropertyName | 用作事件源时间戳的事件属性。 如果未为 timestampPropertyName 指定值，或者指定 null 或空字符串，则会使用事件创建时间。 |
   | eventSourceKeyName | 由时序见解服务用来连接到事件中心的共享访问密钥的名称。 |
   | accessPolicyReaderObjectIds | Azure AD 中应该对环境拥有“读取者”访问权限的用户或应用程序的对象 ID 列表。 可以通过调用 **Get-AzureRMADUser** 或 **Get-AzureRMADServicePrincipal** cmdlet 获取服务主体 objectId。 目前不支持创建 Azure AD 组的访问策略。 |
   | accessPolicyContributorObjectIds | Azure AD 中应该对环境拥有“参与者”访问权限的用户或应用程序的对象 ID 列表。 可以通过调用 **Get-AzureRMADUser** 或 **Get-AzureRMADServicePrincipal** cmdlet 获取服务主体 objectId。 目前不支持创建 Azure AD 组的访问策略。 |

例如，以下参数文件将用于创建环境，以及可从现有事件中心读取事件的事件源。 此外，它会创建两个访问策略，用于授予对环境的“参与者”访问权限。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

有关详细信息，请参阅[参数](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)一文。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>登录到 Azure 并设置 Azure 订阅

在 PowerShell 提示符下，运行以下命令：

```powershell
Connect-AzureRmAccount
```

系统会提示登录到 Azure 帐户。 登录后，运行以下命令以查看可用订阅：

```powershell
Get-AzureRMSubscription
```

此命令返回可用 Azure 订阅的列表。 通过运行以下命令为当前会话选择订阅。 将 `<YourSubscriptionId>` 替换为要使用的 Azure 订阅的 GUID：

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>设置资源组

如果目前没有资源组，请使用 **New-AzureRmResourceGroup** 命令创建新的资源组。 提供资源组的名称，以及要使用的位置。 例如：

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

如果成功，则会显示新的资源组的摘要。

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>测试部署

通过运行 `Test-AzureRmResourceGroupDeployment` cmdlet 验证部署。 测试部署时，请提供与执行部署时所提供的完全相同的参数。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>创建部署

若要创建新部署，请运行 `New-AzureRmResourceGroupDeployment` cmdlet，并在出现提示时提供必需的参数。 参数包括部署的名称、资源组的名称，以及模板文件的路径或 URL。 如果未指定 **Mode** 参数，将使用 **Incremental** 的默认值。 有关详细信息，请参阅[增量部署和完整部署](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)。

以下命令提示在 PowerShell 窗口中输入五个必需的参数：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

若要改为指定参数文件，请使用以下命令：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

运行部署 cmdlet 时，还可以使用内联参数。 该命令如下所示：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

要运行[完整](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)部署，请将 **Mode** 参数设置为 **Complete**：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>验证部署

如果资源已成功部署，会在 PowerShell 窗口中显示部署的摘要：

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>通过 Azure 门户部署快速入门模板

GitHub 上的快速入门模板主页还包含“部署到 Azure”按钮。 单击此按钮可在 Azure 门户中打开“自定义部署”页。 从此页中，可以输入或选择[必需的参数](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters)或[可选参数](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters)表中每个参数的值。 填写设置后，单击“购买”按钮会启动模板部署。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>后续步骤

- 有关使用 REST API 以编程方式管理时序见解资源 的信息，请参阅[时序见解管理](https://docs.microsoft.com/rest/api/time-series-insights-management/)。
