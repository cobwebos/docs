---
title: 用 Azure Monitor 监视 Azure AD B2C
titleSuffix: Azure AD B2C
description: 了解如何使用委托的资源管理来记录 Azure Monitor 的 Azure AD B2C 事件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/03/2020
ms.openlocfilehash: 108c9c1112327a3fcadeff4c4074f31f976a4e3d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026752"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>用 Azure Monitor 监视 Azure AD B2C

使用 Azure Monitor 将 Azure Active Directory B2C （Azure AD B2C）使用情况活动事件路由到不同的监视解决方案。 你可以保留日志以便长期使用或与第三方安全信息和事件管理（SIEM）工具集成，以深入了解环境。

可以将日志事件路由到：

* 一个 Azure 存储帐户。
* 一个 Azure 事件中心（与 Splunk 和 Sumo logic 逻辑实例集成）。
* Azure Log Analytics 工作区（用于分析数据、创建仪表板和针对特定事件的警报）。

![Azure 监视器](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>必备组件

若要完成本文中的步骤，请使用 Azure PowerShell 模块部署 Azure 资源管理器模板。

* [Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)版本6.13.1 或更高版本

你还可以使用[Azure Cloud Shell](https://shell.azure.com)，其中包括 Azure PowerShell 模块的最新版本。

## <a name="delegated-resource-management"></a>委托的资源管理

Azure AD B2C 利用[Azure Active Directory 监视](../active-directory/reports-monitoring/overview-monitoring.md)。 若要在 Azure AD B2C 租户 Azure Active Directory 中启用*诊断设置*，请使用[委派的资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

在 Azure AD B2C 目录（**服务提供商**）中授权用户在包含你的 Azure 订阅（**客户**）的租户中配置 Azure Monitor 实例。 若要创建授权，请将[Azure 资源管理器](../azure-resource-manager/index.yml)模板部署到包含订阅的 Azure AD 租户。 以下各节将引导你完成该过程。

## <a name="create-a-resource-group"></a>创建资源组

在包含你的 Azure 订阅的 Azure Active Directory （Azure AD）租户中（*不*是包含 Azure AD B2C 租户的目录），请[创建一个资源组](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。 使用以下值：

* **订阅**：选择 Azure 订阅。
* **资源组**：输入资源组的名称。 例如， *azure-ad-b2c-监视*。
* **区域**：选择 Azure 位置。 例如“美国中部”。

## <a name="delegate-resource-management"></a>委托资源管理

接下来，收集以下信息：

Azure AD B2C 目录的**目录 id** （也称为租户 id）。

1. 以具有*用户管理员*角色（或更高版本）的用户身份登录到[Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
1. 选择**Azure Active Directory**，选择 "**属性**"。
1. 记录**目录 ID**。

要向其授予*参与者*权限的 Azure AD B2C 组或用户的**对象 ID** ，该资源组是你之前在包含订阅的目录中创建的资源组。

为了更轻松地进行管理，我们建议为每个角色使用 Azure AD 的用户*组*，从而允许你在组中添加或删除单个用户，而不是直接向该用户分配权限。 在本演练中，您将添加一个用户。

1. 在 Azure 门户中**Azure Active Directory**仍处于选中状态，选择 "**用户**"，然后选择用户。
1. 记录用户的**对象 ID**。

### <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

若要加入你的 Azure AD 租户（**客户**），请为你的产品/服务创建一个[Azure 资源管理器模板](../lighthouse/how-to/onboard-customer.md)，其中包含以下信息。 在 Azure 门户的 "[服务提供程序" 页](../lighthouse/how-to/view-manage-service-providers.md)中查看产品/服务详细信息时，`mspOfferName` 和 `mspOfferDescription` 值可见。

| 字段   | 定义 |
|---------|------------|
| `mspOfferName`                     | 描述此定义的名称。 例如， *Azure AD B2C 托管服务*。 此值将作为产品/服务的标题显示给客户。 |
| `mspOfferDescription`              | 产品/服务的简短说明。 例如，*启用 Azure AD B2C 中的 Azure Monitor*。|
| `rgName`                           | 之前在 Azure AD 租户中创建的资源组的名称。 例如， *azure-ad-b2c-监视*。 |
| `managedByTenantId`                | Azure AD B2C 租户的**目录 id** （也称为租户 id）。 |
| `authorizations.value.principalId` | 将有权访问此 Azure 订阅中的资源的 B2C 组或用户的**对象 ID** 。 对于本演练，请指定之前记录的用户的对象 ID。 |

下载 Azure 资源管理器模板和参数文件：

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

接下来，请将参数文件更新为前面记录的值。 下面的 JSON 代码段显示 Azure 资源管理器模板参数文件的示例。 对于 `authorizations.value.roleDefinitionId`，请使用*参与者角色*的[内置角色](../role-based-access-control/built-in-roles.md)值，`b24988ac-6180-42a0-ab88-20f7382dd24c`。

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

更新参数文件后，将 Azure 资源管理器模板部署到 Azure 租户，作为订阅级部署。 由于这是订阅级部署，因此无法在 Azure 门户中启动。 您可以使用 Azure PowerShell 模块或 Azure CLI 来部署。 Azure PowerShell 方法如下所示。

使用[AzAccount](/powershell/azure/authenticate-azureps)登录到包含订阅的目录。 使用 `-tenant` 标志来强制对正确的目录进行身份验证。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

使用[AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 列出当前帐户可在 Azure AD 租户下访问的订阅。 记录要投影到 Azure AD B2C 租户的订阅的 ID。

```PowerShell
Get-AzSubscription
```

接下来，切换到要投影到 Azure AD B2C 租户的订阅：

``` PowerShell
Select-AzSubscription <subscription ID>
```

最后，部署之前下载和更新的 Azure 资源管理器模板和参数文件。 相应地替换 `Location`、`TemplateFile`和 `TemplateParameterFile` 值。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

成功部署模板后，将生成如下所示的输出：

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

部署模板后，可能需要几分钟时间才能完成资源投影。 在转到下一节以选择订阅之前，可能需要等待几分钟（通常不超过5分钟）。

## <a name="select-your-subscription"></a>选择订阅

部署模板并等待几分钟后，资源投影才能完成，请将订阅关联到 Azure AD B2C 目录，并执行以下步骤。

1. 如果当前已登录，**请注销 Azure 门户**。 完成此操作并执行以下步骤，以便在门户会话中刷新凭据。
1. 用 Azure AD B2C 管理帐户登录到[Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标。
1. 选择包含你的订阅的目录。

    ![切换目录](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 验证是否选择了正确的目录和订阅。 在此示例中，选择了所有目录和订阅。

    ![在目录 & 订阅筛选器中选择的所有目录](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>配置诊断设置

委派了资源管理并选择了订阅后，便可以在 Azure 门户中[创建诊断设置](../active-directory/reports-monitoring/overview-monitoring.md)了。

为 Azure AD B2C 活动日志配置监视设置：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
1. 选择**Azure Active Directory**
1. 在“监视”下，选择“诊断设置”。
1. 选择 " **+ 添加诊断设置**"。

    ![Azure 门户中的诊断设置窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

## <a name="next-steps"></a>后续步骤

有关在 Azure Monitor 中添加和配置诊断设置的详细信息，请参阅 Azure Monitor 文档中的以下教程：

[教程：收集和分析 Azure 资源中的资源日志](/azure-monitor/learn/tutorial-resource-logs.md)
