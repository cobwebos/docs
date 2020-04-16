---
title: 使用 Azure 监视器监视 Azure AD B2C
titleSuffix: Azure AD B2C
description: 了解如何使用委派的资源管理使用 Azure 监视器记录 Azure AD B2C 事件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392877"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure 监视器监视 Azure AD B2C

使用 Azure 监视器将 Azure 活动目录 B2C （Azure AD B2C） 登录日志和[审核](view-audit-logs.md)日志路由到不同的监视解决方案。 您可以保留日志以长期使用，或与第三方安全信息和事件管理 （SIEM） 工具集成，从而深入了解您的环境。

您可以将日志事件路由到：

* Azure[存储帐户](../storage/blobs/storage-blobs-introduction.md)。
* Azure[事件中心](../event-hubs/event-hubs-about.md)（并与 Splunk 和相扑逻辑实例集成）。
* [日志分析工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)（用于分析数据、创建仪表板和警报特定事件）。

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>先决条件

要完成本文中的步骤，请使用 Azure PowerShell 模块部署 Azure 资源管理器模板。

* [Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)版本 6.13.1 或更高版本

您还可以使用[Azure 云外壳](https://shell.azure.com)，其中包括最新版本的 Azure PowerShell 模块。

## <a name="delegated-resource-management"></a>委派资源管理

Azure AD B2C 利用[Azure 活动目录监视](../active-directory/reports-monitoring/overview-monitoring.md)。 要在 Azure AD B2C 租户中的 Azure 活动目录中启用*诊断设置*，请使用[委派的资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

您可以授权 Azure AD B2C 目录（**服务提供商**）中的用户或组配置包含 Azure 订阅（**客户**）的租户中的 Azure 监视器实例。 要创建授权，请将 Azure[资源管理器](../azure-resource-manager/index.yml)模板部署到包含订阅的 Azure AD 租户。 以下各节将引导您完成此过程。

## <a name="create-or-choose-resource-group"></a>创建或选择资源组

这是包含目标 Azure 存储帐户、事件中心或日志分析工作区的资源组，用于从 Azure 监视器接收数据。 在部署 Azure 资源管理器模板时指定资源组名称。

在 Azure 活动目录 （Azure AD） 租户中[创建资源组](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)或选择包含 Azure 订阅的现有资源组，*而不是*包含 Azure AD B2C 租户的目录。

本示例*使用位于美国中部*区域的资源组，名为*azure ad-b2c 监视器*。

## <a name="delegate-resource-management"></a>委托资源管理

接下来，收集以下信息：

Azure AD B2C 目录（也称为租户 ID）的**目录 ID。**

1. 使用*用户管理员*角色（或更高版本）以用户身份登录到[Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录****。
1. 选择**Azure 活动目录**，选择**属性**。
1. 记录**目录 ID**。

要向前面在包含订阅的目录中创建的资源组授予*参与者*权限的 Azure AD B2C 组或用户**的对象 ID。**

为了简化管理，我们建议为每个角色使用 Azure AD 用户*组*，允许您向该组添加或删除单个用户，而不是直接向该用户分配权限。 在本演练中，您将添加一个用户。

1. 在 Azure 门户中仍选中**Azure 活动目录**时，请选择 **"用户**"，然后选择用户。
1. 记录用户**的对象 ID**。

### <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

要将 Azure AD 租户（**客户**）上载，请使用以下信息为产品/服务创建[Azure 资源管理器模板](../lighthouse/how-to/onboard-customer.md)。 当您`mspOfferName`在`mspOfferDescription`Azure 门户的["服务提供商"页](../lighthouse/how-to/view-manage-service-providers.md)中查看产品/服务详细信息时，和值可见。

| 字段   | 定义 |
|---------|------------|
| `mspOfferName`                     | 描述此定义的名称。 例如 *，Azure AD B2C 托管服务*。 此值将作为产品/服务的标题显示给客户。 |
| `mspOfferDescription`              | 您的报价的简要说明。 例如，*在 Azure AD B2C 中启用 Azure 监视器*。|
| `rgName`                           | 在 Azure AD 租户中较早之前创建的资源组的名称。 例如 *，azure ad-b2c 监视器*。 |
| `managedByTenantId`                | Azure AD B2C 租户（也称为租户 ID）的**目录 ID。** |
| `authorizations.value.principalId` | B2C 组或用户**的对象 ID，** 这些用户将有权访问此 Azure 订阅中的资源。 在本演练中，指定前面记录的用户对象 ID。 |

下载 Azure 资源管理器模板和参数文件：

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

接下来，使用前面记录的值更新参数文件。 以下 JSON 代码段显示了 Azure 资源管理器模板参数文件的示例。 对于`authorizations.value.roleDefinitionId`，使用 *"参与者"角色的*`b24988ac-6180-42a0-ab88-20f7382dd24c`[内置角色](../role-based-access-control/built-in-roles.md)值 。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure 资源管理器模板

更新参数文件后，将 Azure 资源管理器模板作为订阅级部署部署到 Azure 租户中。 由于这是订阅级部署，因此无法在 Azure 门户中启动。 可以使用 Azure PowerShell 模块或 Azure CLI 进行部署。 Azure PowerShell 方法如下所示。

使用[Connect-AzAccount](/powershell/azure/authenticate-azureps)登录到包含订阅的目录。 使用标志`-tenant`强制对正确的目录进行身份验证。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

使用[获取-Az订阅](/powershell/module/az.accounts/get-azsubscription)cmdlet 列出当前帐户在 Azure AD 租户下可以访问的订阅。 记录要投影到 Azure AD B2C 租户中的订阅的 ID。

```PowerShell
Get-AzSubscription
```

接下来，切换到要投影到 Azure AD B2C 租户的订阅：

``` PowerShell
Select-AzSubscription <subscription ID>
```

最后，部署之前下载和更新的 Azure 资源管理器模板和参数文件。 相应地替换`Location``TemplateFile`和`TemplateParameterFile`值。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

成功部署模板可生成类似于以下内容的输出（输出为简洁而截断）：

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

部署模板后，可能需要几分钟才能完成资源投影。 您可能需要等待几分钟（通常不超过五个），然后进入下一节才能选择订阅。

## <a name="select-your-subscription"></a>选择订阅

部署模板并等待几分钟后资源投影完成后，将订阅与 Azure AD B2C 目录相关联，并执行以下步骤。

1. 如果您当前已登录，请**注销**Azure 门户。 执行此步骤和以下步骤，以在门户会话中刷新凭据。
1. 使用 Azure AD B2C 管理帐户登录到[Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”**** 图标。
1. 选择包含订阅的目录。

    ![切换目录](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 验证您选择了正确的目录和订阅。 在此示例中，将选择所有目录和订阅。

    ![在目录&订阅筛选器中选择的所有目录](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>配置诊断设置

诊断设置定义应发送资源的日志和指标的位置。 可能的目的地是：

- [Azure 存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)
- [事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)解决方案。
- [Log Analytics 工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)

如果尚未，请在[Azure 资源管理器模板](#create-an-azure-resource-manager-template)中指定的资源组中创建所选目标类型的实例。

### <a name="create-diagnostic-settings"></a>创建诊断设置

您已准备好在 Azure 门户中[创建诊断设置](../active-directory/reports-monitoring/overview-monitoring.md)。

要配置 Azure AD B2C 活动日志的监视设置，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录****。
1. 选择**Azure 活动目录**
1. 在“监视”下，选择“诊断设置”********。
1. 如果资源上有现有的设置，则会看到已配置的设置列表。 选择 **"添加诊断设置**以添加新设置"，或**选择"编辑**"设置以编辑现有设置。 每个设置只能具有每个目标类型的一个。

    ![Azure 门户中的诊断设置窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 为设置指定名称（如果未指定）。
1. 选中要将日志发送到的每个目标对应的框。 选择 **"配置**"以按下表中所述指定其设置。

    | 设置 | 说明 |
    |:---|:---|
    | 存档到存储帐户 | 存储帐户的名称。 |
    | 流式传输到事件中心 | 要在其中创建事件中心的命名空间（如果这是首次流式传输日志）或要将日志流式传输到的命名空间（如果已有资源将该日志类别流式传输到此命名空间）。
    | 发送到 Log Analytics | 工作区的名称。 |

1. 选择**审核日志**和**登录日志**。
1. 选择“保存”。 

## <a name="next-steps"></a>后续步骤

有关在 Azure 监视器中添加和配置诊断设置的详细信息，请参阅[教程：从 Azure 资源收集和分析资源日志](../azure-monitor/insights/monitor-azure-resource.md)。

有关将 Azure AD 日志流式传输到事件中心的信息，请参阅[教程：将 Azure 活动目录日志流式传输到 Azure 事件中心](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
