---
title: 在 Azure 自动化中使用 Azure AD 以便向 Azure 进行身份验证
description: 了解如何使用 Azure 自动化中的 Azure AD 作为向 Azure 进行身份验证的提供程序。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548343"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>在 Azure 自动化中使用 Azure AD 以便向 Azure 进行身份验证

[Azure Active Directory （AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)服务支持大量管理任务，例如用户管理、域管理和单一登录配置。 本文介绍如何使用 Azure 自动化中的 Azure AD 作为向 Azure 进行身份验证的提供程序。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="installing-azure-ad-modules"></a>安装 Azure AD 模块

可以通过以下 PowerShell 模块启用 Azure AD：

* Graph Azure Active Directory PowerShell （AzureRM 和 Az 模块）。 Azure Automation 随附了 AzureRM 模块及其最近的升级，Az 模块。 功能包括使用 Azure AD 用户（OrgId）基于凭据的身份验证向 Azure 进行非交互式身份验证。 请参阅[Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)。

* 适用于 Windows PowerShell 的 Microsoft Azure Active Directory （MSOnline 模块）。 此模块启用与 Microsoft Online （包括 Office 365）的交互。

>[!NOTE]
>PowerShell Core 不支持 MSOnline 模块。 若要使用 module cmdlet，必须从 Windows PowerShell 运行它们。 建议使用较新的 Azure Active Directory PowerShell for Graph 模块，而不是使用 MSOnline 模块。 

### <a name="preinstallation"></a>预安装

在计算机上安装 Azure AD 模块之前：

* 卸载任何以前版本的 AzureRM/Az 模块和 MSOnline 模块。 

* 卸载 Microsoft Online Services 登录助手以确保正确操作新的 PowerShell 模块。  

### <a name="install-the-azurerm-and-az-modules"></a>安装 AzureRM 和 Az 模块

>[!NOTE]
>若要使用这些模块，必须使用 PowerShell 5.1 版或更64高版本的 Windows。 

1. 安装 Windows Management Framework （WMF）5.1。 请参阅[安装和配置 WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 使用在[Windows 上使用 PowerShellGet 安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)中的说明安装 AzureRM 和/或 Az。

### <a name="install-the-msonline-module"></a>安装 MSOnline 模块

>[!NOTE]
>若要安装 MSOnline 模块，你必须是 Office 365 管理员角色的成员。 请参阅[关于管理员角色](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。

1. 确保在您的计算机上启用 Microsoft .NET Framework 5.x 版功能。 您的计算机可能已安装了较新版本，但可以启用或禁用向后兼容 .NET Framework 的旧版本。 

2. 安装[Microsoft Online Services 登录助手](https://www.microsoft.com/download/details.aspx?id=41950)的64位版本。

3. 以管理员身份运行 Windows PowerShell，以创建提升的 Windows PowerShell 命令提示符。

4. 从[MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)部署 Azure Active Directory。

5. 如果系统提示你安装 NuGet 提供程序，请键入 Y，然后按 ENTER。

6. 如果系统提示您从[PSGallery](https://www.powershellgallery.com/)安装模块，请键入 Y，然后按 enter。

### <a name="install-support-for-pscredential"></a>安装对 PSCredential 的支持

Azure Automation 使用[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)类来表示凭据资产。 脚本使用`Get-AutomationPSCredential` cmdlet `PSCredential`检索对象。 有关详细信息，请参阅[Azure 自动化中的凭据资产](shared-resources/credentials.md)。

## <a name="assigning-a-subscription-administrator"></a>分配订阅管理员

必须分配 Azure 订阅的管理员。 此用户具有订阅范围的所有者角色。 请参阅[Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。 

## <a name="changing-the-azure-ad-users-password"></a>更改 Azure AD 用户的密码

若要更改 Azure AD 用户的密码：

1. 注销 Azure。

2. 让管理员以用户刚刚创建的 Azure AD 用户身份登录到 Azure，使用完整的用户名（包括域）和临时密码。 

3. 要求管理员在出现提示时更改密码。

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>将 Azure 自动化配置为使用 Azure AD 用户管理 Azure 订阅

要使 Azure 自动化与 Azure AD 通信，必须检索与 Azure 连接关联的凭据以 Azure AD。 这些凭据的示例包括租户 ID、订阅 ID 等。 有关 Azure 与 Azure AD 之间连接的详细信息，请参阅[将组织连接到 Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)。

## <a name="creating-a-credential-asset"></a>创建凭据资产

使用适用于 Azure AD 的 Azure 凭据，可以创建一个 Azure 自动化凭据资产，以安全地存储 Azure AD 凭据，以便 runbook 和所需状态配置（DSC）脚本可以访问它们。 可以使用 Azure 门户或 PowerShell cmdlet 来执行此操作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 门户中创建凭据资产

你可以使用 Azure 门户来创建凭据资产。 使用**共享资源**下的**凭据**从自动化帐户执行此操作。 请参阅[Azure 自动化中的凭据资产](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>通过 Windows PowerShell 创建凭据资产

若要在 Windows PowerShell 中准备新的凭据资产，你的脚本`PSCredential`首先使用指定的用户名和密码创建一个对象。 然后，该脚本使用此对象通过调用[AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet 来创建资产。 或者，该脚本可以调用[获取凭据](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)cmdlet，以提示用户键入名称和密码。 请参阅[Azure 自动化中的凭据资产](shared-resources/credentials.md)。 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>从 Azure 自动化 runbook 管理 Azure 资源

可以使用凭据资产从 Azure 自动化 runbook 管理 Azure 资源。 下面是一个示例 PowerShell runbook，用于收集用于在 Azure 订阅中停止和启动虚拟机的凭据资产。 此 runbook 首先使用`Get-AutomationPSCredential`来检索用于向 Azure 进行身份验证的凭据。 然后，它调用[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet 以使用凭据连接到 Azure。 此脚本使用[get-azuresubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) cmdlet 来选择要使用的订阅。 

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

* 可以在[Azure 自动化中的凭据资产](shared-resources/credentials.md)中找到有关自动化凭据资产的信息。
* 请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)以了解如何使用自动化模块。
* 若要详细了解可用于在 Azure 自动化中启动 runbook 的方法，请参阅[在 Azure 自动化中启动 runbook](automation-starting-a-runbook.md)。
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。