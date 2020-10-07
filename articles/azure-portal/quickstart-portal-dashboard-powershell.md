---
title: 使用 PowerShell 创建 Azure 门户仪表板
description: 了解如何使用 Azure PowerShell 在 Azure 门户中创建仪表板。
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87440693"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>快速入门：使用 PowerShell 创建 Azure 门户仪表板

Azure 门户中的仪表板可以集中且有组织地呈现你的云资源。 本文重点介绍使用 Az.Portal PowerShell 模块创建仪表板的过程。
仪表板会显示虚拟机 (VM) 的性能以及一些静态信息和链接。

## <a name="requirements"></a>要求

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 尽管 Az.Portal PowerShell 模块为预览版，但必须使用 `Install-Module` cmdlet 从 Az PowerShell 模块单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>选择特定 Azure 订阅

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>定义变量

你将重复使用几条信息。 创建变量以存储信息。

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

下面的示例创建一个资源组，该资源组基于在 `$location` 变量中指定的区域中 `$resourceGroupName` 变量中的名称。

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

在本快速入门的下一部分中创建的仪表板需要一台现有 VM。 按照以下步骤创建 VM。

将 VM 的登录凭据存储在一个变量中。 密码必须是复杂密码。 这是一个新的用户名和密码；它不是用于登录 Azure 的帐户。 有关详细信息，请参阅[用户名要求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)和[密码要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

```azurepowershell-interactive
$Cred = Get-Credential
```

创建 VM。

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

VM 部署现在将开始进行，通常需要几分钟才能完成。 部署完成后，请转到下一部分。

## <a name="download-the-dashboard-template"></a>下载仪表板模板

由于 Azure 仪表板是资源，所以它们可以表示为 JSON。 下面的代码会下载示例仪表板的 JSON 表示形式。 有关详细信息，请参阅 [Azure 仪表板结构](/azure/azure-portal/azure-portal-dashboards-structure)。

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>自定义模板

通过运行以下代码来自定义下载的模板。

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

有关详细信息，请参阅 [Microsoft 门户仪表板模板参考](/azure/templates/microsoft.portal/dashboards)。

## <a name="deploy-the-dashboard-template"></a>部署仪表板模板

可以使用 Az.Portal 模块中的 `New-AzPortalDashboard` cmdlet 直接从 PowerShell 部署模板。

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>查看已部署的资源

检查仪表板是否已成功创建。

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

验证是否可以在 Azure 门户中查看有关 VM 的数据。

1. 在 Azure 门户中，选择“仪表板”。

   ![Azure 门户导航到仪表板](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. 在仪表板页面上，选择“简单的 VM 仪表板”。

   ![导航到“简单的 VM 仪表板”](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. 查看仪表板。 你可以看到某些内容是静态的，但也有一些图表显示 VM 的性能。

   ![查看简单的 VM 仪表板](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>清理资源

若要删除 VM 和关联仪表板，请删除包含它们的资源组。

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

有关 Az.Portal PowerShell 模块中包含的 cmdlet 的详细信息，请参阅：

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell：门户仪表板 cmdlet](https://docs.microsoft.com/powershell/module/Az.Portal/)
