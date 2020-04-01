---
title: 在 Azure 自动化中使用 Azure AD 对 Azure 进行身份验证
description: 了解如何在 Azure 自动化中使用 Azure AD 作为对 Azure 进行身份验证的提供程序。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479443"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>在 Azure 自动化中使用 Azure AD 对 Azure 进行身份验证

[Azure 活动目录 （AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)服务支持许多管理任务，如用户管理、域管理和单一登录配置。 本文介绍如何在 Azure 自动化中使用 Azure AD 作为对 Azure 进行身份验证的提供程序。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="installing-azure-ad-modules"></a>安装 Azure AD 模块

您可以通过以下 PowerShell 模块启用 Azure AD：

* 用于图形的 Azure 活动目录电源外壳（AzureRM 和 Az 模块）。 Azure 自动化随 AzureRM 模块及其最近的升级（Az 模块）一起提供。 功能包括使用 Azure AD 用户 （OrgId） 基于凭据的身份验证对 Azure 的非交互式身份验证。 请参阅[Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)。

* 适用于 Windows 电源外壳（MSOnline 模块）的 Microsoft Azure 活动目录。 此模块支持与 Microsoft 在线（包括 Office 365）的交互。

>[!NOTE]
>PowerShell Core 不支持 MSOnline 模块。 要使用模块 cmdlet，必须从 Windows PowerShell 运行它们。 我们鼓励您将较新的 Azure 活动目录 PowerShell 用于图形模块，而不是 MSOnline 模块。 

### <a name="preinstallation"></a>预安装

在计算机上安装 Azure AD 模块之前：

* 卸载 AzureRM/Az 模块和 MSOnline 模块的任何早期版本。 

* 卸载 Microsoft 在线服务登录助手，以确保新 PowerShell 模块的正确操作。  

### <a name="install-the-azurerm-and-az-modules"></a>安装 AzureRM 和 Az 模块

>[!NOTE]
>要使用这些模块，您必须将 PowerShell 版本 5.1 或更高版本与 64 位版本的 Windows 使用。 

1. 安装 Windows 管理框架 （WMF） 5.1。 请参阅[安装和配置 WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 使用在[Windows 上使用 PowerShellGet 安装 AzureRm](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)和/或 Az 的说明。

### <a name="install-the-msonline-module"></a>安装 MSOnline 模块

>[!NOTE]
>要安装 MSOnline 模块，您必须是 Office 365 管理员角色的成员。 请参阅[有关管理员角色](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。

1. 确保计算机上启用了 Microsoft .NET 框架 3.5.x 功能。 您的计算机可能安装了较新版本，但可以启用或禁用与旧版本的 .NET Framework 的向后兼容性。 

2. 安装 64 位版本的[Microsoft 在线服务登录助手](https://www.microsoft.com/download/details.aspx?id=41950)。

3. 以管理员身份运行 Windows PowerShell 以创建提升的 Windows PowerShell 命令提示符。

4. 从[MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)部署 Azure 活动目录。

5. 如果系统提示您安装 NuGet 提供程序，请键入 Y 并按 ENTER。

6. 如果系统提示您从[PSGallery](https://www.powershellgallery.com/)安装模块，请键入 Y 并按 ENTER。

### <a name="install-support-for-pscredential"></a>安装对 PS 凭据的支持

Azure 自动化使用[PS凭据](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)类表示凭据资产。 脚本使用`PSCredential``Get-AutomationPSCredential`cmdlet 检索对象。 有关详细信息，请参阅[Azure 自动化 中的凭据资产](shared-resources/credentials.md)。

## <a name="assigning-a-subscription-administrator"></a>分配订阅管理员

您必须为 Azure 订阅分配管理员。 此人具有订阅作用域的所有者角色。 请参阅[Azure 自动化 中的基于角色的访问控制](automation-role-based-access-control.md)。 

## <a name="changing-the-azure-ad-users-password"></a>更改 Azure AD 用户的密码

要更改 Azure AD 用户的密码，请执行以下操作：

1. 注销 Azure。

2. 让管理员在 Azure AD 用户刚刚创建时使用完整用户名（包括域）和临时密码登录到 Azure。 

3. 在出现提示时要求管理员更改密码。

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>配置 Azure 自动化以使用 Azure AD 用户来管理 Azure 订阅

若要与 Azure 自动化通信，必须检索与 Azure 连接到 Azure AD 关联的凭据。 这些凭据的示例包括租户 ID、订阅 ID 等。 有关 Azure 和 Azure AD 之间的连接，请参阅[将组织连接到 Azure 活动目录](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)。

## <a name="creating-a-credential-asset"></a>创建凭据资产

使用 Azure AD 的 Azure 凭据后，是时候创建 Azure 自动化凭据资产来安全地存储 Azure AD 凭据，以便 Runbook 和欲望状态配置 （DSC） 脚本可以访问它们。 可以使用 Azure 门户或 PowerShell cmdlet 执行此操作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 门户中创建凭据资产

可以使用 Azure 门户创建凭据资产。 使用**共享资源**下的**凭据**从自动化帐户执行此操作。 请参阅[Azure 自动化 中的凭据资产](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建凭据资产

要在 Windows PowerShell 中准备新的凭据资产，脚本首先`PSCredential`使用分配的用户名和密码创建对象。 然后，脚本使用此对象通过调用[New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet 来创建资产。 或者，脚本可以调用[Get-凭据](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)cmdlet 以提示用户输入用户名和密码。 请参阅[Azure 自动化 中的凭据资产](shared-resources/credentials.md)。 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>从 Azure 自动化运行簿管理 Azure 资源

您可以使用凭据资产从 Azure 自动化运行簿管理 Azure 资源。 下面是 PowerShell 运行簿的示例，用于收集用于在 Azure 订阅中停止和启动虚拟机的凭据资产。 此 Runbook`Get-AutomationPSCredential`首先用于检索用于对 Azure 进行身份验证的凭据。 然后，它调用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet 以使用凭据连接到 Azure。 该脚本使用[选择 Azure 订阅](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0)cmdlet 选择要使用的订阅。 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>后续步骤

* 您可以在[Azure 自动化 中的凭据资产中找到](shared-resources/credentials.md)有关自动化凭据资产的信息。
* 请参阅[管理 Azure 自动化中的模块](shared-resources/modules.md)，了解如何使用自动化模块。
* 要了解有关可用于在 Azure 自动化中启动 Runbook 的方法，请参阅[在 Azure 自动化 中启动 Runbook。](automation-starting-a-runbook.md)
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。