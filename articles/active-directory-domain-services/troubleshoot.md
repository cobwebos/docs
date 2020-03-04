---
title: Azure Active Directory 域服务故障排除 |Microsoft Docs "
description: 了解如何在创建或管理 Azure Active Directory 域服务时排查常见错误
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4b2dea05b459d6e9ae4eb086fa127d88a84a768
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249209"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的常见错误和故障排除步骤

作为应用程序的标识和身份验证的核心部分，Azure Active Directory 域服务（Azure AD DS）有时会出现问题。 如果遇到问题，会出现一些常见的错误消息和相关的故障排除步骤，以帮助你再次运行操作。 你还可以随时[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

本文提供 Azure AD DS 中常见问题的疑难解答步骤。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>无法为 Azure AD 目录启用 Azure AD 域服务

如果在启用 Azure AD DS 时遇到问题，请查看以下常见错误和解决这些错误的步骤：

| **示例错误消息** | **解决方法** |
| --- |:--- |
| *此网络上已在使用该名称 addscontoso.com。指定一个未使用的名称。* |[虚拟网络中的域名冲突](troubleshoot.md#domain-name-conflict) |
| *无法在此 Azure AD 租户中启用域服务。服务对名为 "Azure AD 域服务同步" 的应用程序没有足够的权限。删除名为 "Azure AD 域服务同步" 的应用程序，然后尝试为 Azure AD 租户启用域服务。* |[域服务没有足够的权限访问 Azure AD 域服务同步应用程序](troubleshoot.md#inadequate-permissions) |
| *无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有启用域服务所需的权限。使用应用程序标识符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 删除应用程序，然后尝试为 Azure AD 租户启用域服务。* |[Azure AD 租户中未正确配置域服务应用程序](troubleshoot.md#invalid-configuration) |
| *无法在此 Azure AD 租户中启用域服务。Azure AD 租户中禁用 Microsoft Azure AD 应用程序。启用应用程序标识符为 00000002-0000-0000-c000-000000000000 的应用程序，然后尝试为 Azure AD 租户启用域服务。* |[Microsoft Graph 应用程序已在 Azure AD 租户中禁用](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>域名冲突

**错误消息**

*此网络上已在使用该名称 aaddscontoso.com。指定一个未使用的名称。*

**解决方法**

检查相同的或对等互连的虚拟网络上没有具有相同域名的现有 AD DS 环境。 例如，你可能有一个在 Azure Vm 上运行的名为*aaddscontoso.com*的 AD DS 域。 尝试在虚拟网络上启用具有相同域名*aaddscontoso.com*的 Azure AD DS 托管域时，请求的操作将失败。

此失败是由虚拟网络上的域名冲突引起的。 DNS 查找检查现有 AD DS 环境是否响应请求的域名。 若要解决此失败，请使用其他名称设置 Azure AD DS 托管域，或取消预配现有 AD DS 域，然后重试以启用 Azure AD DS。

### <a name="inadequate-permissions"></a>权限不足

**错误消息**

*无法在此 Azure AD 租户中启用域服务。服务对名为 "Azure AD 域服务同步" 的应用程序没有足够的权限。删除名为 "Azure AD 域服务同步" 的应用程序，然后尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查 Azure AD 目录中是否存在名为*Azure AD 域服务同步*的应用程序。 如果此应用程序存在，请将其删除，然后重试启用 Azure AD DS。 若要检查现有的应用程序并根据需要删除该应用程序，请完成以下步骤：

1. 在 Azure 门户中，从左侧导航菜单中选择 " **Azure Active Directory** "。
1. 选择“企业应用程序”。 从 "**应用程序类型**" 下拉菜单中选择 "*所有应用程序*"，然后选择 "**应用**"。
1. 在搜索框中，输入 " *Azure AD 域服务同步*"。如果该应用程序存在，请将其选中，然后选择 "**删除**"。
1. 删除应用程序后，请尝试再次启用 Azure AD DS。

### <a name="invalid-configuration"></a>配置无效

**错误消息**

*无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有启用域服务所需的权限。使用应用程序标识符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 删除应用程序，然后尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查 Azure AD 目录中是否有一个名为*AzureActiveDirectoryDomainControllerServices*的现有应用程序，其应用程序标识符为*d87dcbc6-a371-462e-88e3-28ad15ec4e64* 。 如果此应用程序存在，请将其删除，然后重试启用 Azure AD DS。

使用以下 PowerShell 脚本搜索现有的应用程序实例，并在需要时将其删除：

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 已禁用

**错误消息**

*无法在此 Azure AD 租户中启用域服务。Azure AD 租户中禁用 Microsoft Azure AD 应用程序。启用应用程序标识符为 00000002-0000-0000-c000-000000000000 的应用程序，然后尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查是否已禁用了标识符为*00000002-0000-0000-c000-000000000000*的应用程序。 此应用程序是一个 Microsoft Azure AD 应用程序，为图形 API 提供对 Azure AD 租户的访问权限。 若要同步 Azure AD 租户，必须启用此应用程序。

若要查看此应用程序的状态并根据需要启用它，请完成以下步骤：

1. 在 Azure 门户中，从左侧导航菜单中选择 " **Azure Active Directory** "。
1. 选择“企业应用程序”。 从 "**应用程序类型**" 下拉菜单中选择 "*所有应用程序*"，然后选择 "**应用**"。
1. 在搜索框中，输入 " *00000002-0000-0000-c000-00000000000*"。 选择应用程序，然后选择 "**属性**"。
1. 如果 "**允许用户登录**" 设置为 "*否*"，则将值设置为 *"是"* ，然后选择 "**保存**"。
1. 启用应用程序后，请尝试再次启用 Azure AD DS。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>用户无法登录 Azure AD 域服务托管域

如果 Azure AD 租户中的一个或多个用户无法登录到 Azure AD DS 托管域，请完成以下故障排除步骤：

* **凭据格式**-尝试使用 UPN 格式指定凭据，如 `dee@aaddscontoso.onmicrosoft.com`。 UPN 格式是在 Azure AD DS 中指定凭据的建议方法。 请确保在 Azure AD 中正确配置了此 UPN。

    如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，则可能会自动生成帐户的*SAMAccountName* （如*AADDSCONTOSO\driley* ）。 因此，你的帐户的*SAMAccountName*格式可能不同于你在本地域中的预期或使用的格式。

* **密码同步**-请确保已为[仅限云的用户][cloud-only-passwords]或[使用 Azure AD Connect 的混合环境][hybrid-phs]启用密码同步。
    * **混合同步帐户：** 如果受影响的用户帐户是从本地目录同步的，请验证以下方面：
    
      * 你已部署或更新到[Azure AD Connect 的最新建议版本](https://www.microsoft.com/download/details.aspx?id=47594)。
      * 已将 Azure AD Connect 配置为[执行完全同步][hybrid-phs]。
      * 根据目录的大小，可能需要一段时间，用户帐户和凭据哈希在 Azure AD DS 中可用。 在尝试对托管域进行身份验证之前，请确保等待足够长的时间。
      * 如果在验证前面的步骤后该问题仍然存在，请尝试重启*Microsoft Azure AD 同步服务*。 在 Azure AD Connect 服务器上，打开命令提示符并运行以下命令：
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **仅限云的帐户**：如果受影响的用户帐户是仅限云的用户帐户，请确保在[启用 Azure AD DS 后，用户已更改其密码][cloud-only-passwords]。 此密码重置将导致生成 Azure AD 域服务所需的凭据哈希。

* **验证用户帐户是否处于活动状态**：默认情况下，在2分钟内，托管域上的5次无效密码尝试将导致用户帐户被锁定30分钟。 当帐户被锁定时，用户无法登录。30分钟后，用户帐户会自动解锁。
  * Azure AD DS 托管域上的密码尝试无效，不会锁定 Azure AD 中的用户帐户。 仅在托管域中锁定用户帐户。 使用[管理 VM][management-vm]（而不是在 Azure AD 中 *）检查 Active Directory 管理控制台（ADAC）* 中的用户帐户状态。
  * 你还可以[配置细化密码策略][password-policy]，以更改默认锁定阈值和持续时间。

* **外部帐户**-检查受影响的用户帐户不是 Azure AD 租户中的外部帐户。 外部帐户的示例包括 Microsoft 帐户（如 `dee@live.com`）或外部 Azure AD 目录中的用户帐户。 Azure AD DS 不存储外部用户帐户的凭据，因此无法登录到托管域。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>托管域上出现一个或多个警报

如果 Azure AD DS 托管域上存在活动警报，可能会阻止身份验证过程正常运行。

若要查看是否有任何活动警报，请[检查 AZURE AD DS 托管域的运行状况状态][check-health]。 如果显示任何警报，请[对其进行故障排除和解决][troubleshoot-alerts]。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>从 Azure AD 租户中删除的用户不会从托管域中删除

Azure AD 可防止意外删除用户对象。 从 Azure AD 租户中删除用户帐户时，会将相应的用户对象移动到回收站。 将此删除操作同步到 Azure AD DS 托管域时，相应的用户帐户将标记为禁用。 此功能可帮助您恢复或撤消删除用户帐户。

即使在 Azure AD 目录中重新创建了具有相同 UPN 的用户帐户，用户帐户仍在 Azure AD DS 托管域中处于禁用状态。 若要从 Azure AD DS 托管域中删除用户帐户，需要从 Azure AD 租户中强制删除该帐户。

若要从 Azure AD DS 托管域完全删除用户帐户，请使用带有 `-RemoveFromRecycleBin` 参数的[Set-msoluser][Remove-MsolUser] PowerShell cmdlet 从 Azure AD 租户中永久删除该用户。

## <a name="next-steps"></a>后续步骤

如果继续遇到问题，请[提出 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
