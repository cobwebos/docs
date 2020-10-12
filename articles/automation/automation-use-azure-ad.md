---
title: 在 Azure 自动化中使用 Azure AD 以便向 Azure 进行身份验证
description: 本文介绍如何使用 Azure 自动化中的 Azure AD 作为向 Azure 进行身份验证的提供程序。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: bbd1f977b548c2b8e7980709ea125c07e22b1fa2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400666"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>使用 Azure AD 向 Azure 进行身份验证

[Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) 服务支持多种管理任务，例如用户管理、域管理和单一登录配置。 本文介绍如何使用 Azure 自动化中的 Azure AD 作为向 Azure 进行身份验证的提供程序。 

## <a name="install-azure-ad-modules"></a>安装 Azure AD 模块

可通过以下 PowerShell 模块启用 Azure AD：

* 用于 Graph 的 Azure Active Directory PowerShell（AzureRM 和 Az 模块）。 Azure 自动化随附了 AzureRM 模块及其最新的升级，Az 模块。 功能包括使用 Azure AD 用户 (OrgId) 基于凭据的身份验证向 Azure 进行非交互式身份验证。 请参阅 [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)。

* 用于 Windows PowerShell 的 Microsoft Azure Active Directory（MSOnline 模块）。 此模块启用与 Microsoft Online 的交互，包括 Microsoft 365。

>[!NOTE]
>PowerShell Core 不支持 MSOnline 模块。 若要使用模块 cmdlet，必须从 Windows PowerShell 运行这些 cmdlet。 建议使用较新的用于 Graph 的 Azure Active Directory PowerShell 模块，而不是使用 MSOnline 模块。 

### <a name="preinstallation"></a>预安装

在计算机上安装 Azure AD 模块之前：

* 卸载任何以前版本的 AzureRM/Az 模块和 MSOnline 模块。 

* 卸载 Microsoft Online Services 登录助手以确保正确操作新的 PowerShell 模块。  

### <a name="install-the-azurerm-and-az-modules"></a>安装 AzureRM 和 Az 模块

>[!NOTE]
>若要使用这些模块，必须将 PowerShell 版本 5.1 或更高版本与 64 位版本 Windows 一起使用。 

1. 安装 Windows Management Framework (WMF) 5.1。 请参阅[安装和配置 WMF 5.1](/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 请按照[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0) 中的说明安装 AzureRM 和/或 Az。

### <a name="install-the-msonline-module"></a>安装 MSOnline 模块

>[!NOTE]
>若要安装 MSOnline 模块，你必须是管理员角色的成员。 请参阅[关于管理员角色](/microsoft-365/admin/add-users/about-admin-roles)。

1. 请确保已在计算机上启用 Microsoft .NET Framework 3.5.x 功能。 计算机可能已安装较新版本，但可启用或禁用较旧版本的 .NET Framework 的后向兼容性。 

2. 安装 64 位版本的 [Microsoft Online Services 登录助手](https://www.microsoft.com/download/details.aspx?id=41950)。

3. 以管理员身份运行 Windows PowerShell，创建提升的 Windows PowerShell 命令提示符。

4. 从 [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0) 部署 Azure Active Directory。

5. 如果系统提示安装 NuGet 提供程序，请键入“Y”，然后按 Enter。

6. 如果系统提示从 [PSGallery](https://www.powershellgallery.com/) 中安装模块，请键入“Y”，然后按 Enter。

### <a name="install-support-for-pscredential"></a>对 PSCredential 的安装支持

Azure 自动化使用 [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 类表示凭据资产。 脚本使用 `Get-AutomationPSCredential` cmdlet 检索 `PSCredential` 对象。 有关详细信息，请参阅 [Azure 自动化中的凭据资产](shared-resources/credentials.md)。

## <a name="assign-a-subscription-administrator"></a>分配订阅管理员

必须为 Azure 订阅分配一个管理员。 此管理员拥有订阅范围的所有者角色。 请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。 

## <a name="change-the-azure-ad-users-password"></a>更改 Azure AD 用户的密码

更改 Azure AD 用户的密码：

1. 从 Azure 注销。

2. 让管理员使用完整的用户名（包括域）和临时密码，以刚刚创建的 Azure AD 用户身份登录到 Azure。 

3. 让管理员在出现提示时更改密码。

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>配置 Azure 自动化以管理 Azure 订阅

若要使 Azure 自动化与 Azure AD 通信，必须检索与 Azure 和 Azure AD 之间的连接相关联的凭据。 这些凭据的示例包括租户 ID、订阅 ID 等。 有关 Azure 与 Azure AD 之间连接的详细信息，请参阅[将组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)。

## <a name="create-a-credential-asset"></a>创建凭据资产

具有用于 Azure AD 的 Azure 凭据之后，可以创建一个用于安全存储 Azure AD 凭据的 Azure 自动化凭据资产，以便 runbook 和 Desired State Configuration (DSC) 脚本可以访问这些凭据。 可以使用 Azure 门户或 PowerShell cmdlet 来执行此操作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 门户中创建凭据资产

可以使用 Azure 门户来创建凭据资产。 使用“共享资源”下的“凭据”，从自动化帐户执行此操作 。 请参阅 [Azure 自动化中的凭据资产](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建凭据资产

若要在 Windows PowerShell 中准备新的凭据资产，脚本应首先使用分配的用户名和密码创建 `PSCredential` 对象。 然后，脚本使用此对象通过调用 [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet 来创建资产。 或者，脚本可以调用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) cmdlet 来提示用户键入名称和密码。 请参阅 [Azure 自动化中的凭据资产](shared-resources/credentials.md)。 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>通过 Azure 自动化 runbook 管理 Azure 资源

可以使用凭据资产通过 Azure 自动化 runbook 管理 Azure 资源。 下面是一个示例 PowerShell runbook，它收集用于停止和启动 Azure 订阅中的虚拟机的凭据资产。 此 runbook 首先使用 `Get-AutomationPSCredential` 来检索用于向 Azure 进行身份验证的凭据。 然后，它会调用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet，以使用凭据连接到 Azure。 此脚本使用 [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription?view=azuresmps-4.0.0) cmdlet 来选择要使用的订阅。 

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

* 有关凭据用法的详细信息，请参阅[在 Azure 自动化中管理凭据](shared-resources/credentials.md)。
* 有关模块的信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。
* 如果需要启动 runbook，请参阅[在 Azure 自动化中启动 runbook](start-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
