---
title: Azure Active Directory 域服务故障排除 | Microsoft Docs
description: 了解如何排查创建或管理 Azure Active Directory 域服务时的常见错误
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 9593fe71fc4a29678d58d7c67699210a4a39f95e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967369"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的常见错误和故障排除步骤

作为应用程序的标识和身份验证的核心部分，Azure Active Directory 域服务 (Azure AD DS) 有时会出现问题。 如果遇到问题，请查看这里列出的一些常见错误消息以及可帮助你使服务再次运行的相关故障排除步骤。 你还可以随时[提交 Azure 支持请求][azure-support]以获得额外的排除故障帮助。

本文提供了 Azure AD DS 中的常见问题的故障排除步骤。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>无法为 Azure AD 目录启用 Azure AD 域服务

如果在启用 Azure AD DS 时遇到问题，请查看以下常见错误和解决这些错误的步骤：

| **示例错误消息** | **解决方法** |
| --- |:--- |
| *名称 aaddscontoso.com 已在此网络中使用。指定一个未使用的名称。* |[虚拟网络中的域名冲突](troubleshoot.md#domain-name-conflict) |
| *无法在此 Azure AD 租户中启用域服务。该服务对名为“Azure AD 域服务同步”的应用程序没有足够的权限。请删除名为“Azure AD 域服务同步”的应用程序，并尝试为 Azure AD 租户启用域服务。* |[域服务对 Azure AD 域服务同步应用程序没有足够的权限](troubleshoot.md#inadequate-permissions) |
| *无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有所需的权限来启用域服务。请删除标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的应用程序，并尝试为 Azure AD 租户启用域服务。* |[未在 Azure AD 租户中正确配置域服务应用程序](troubleshoot.md#invalid-configuration) |
| *无法在此 Azure AD 租户中启用域服务。Microsoft Azure AD 应用程序已在 Azure AD 租户中禁用。请启用标识符为 00000002-0000-0000-c000-000000000000 的应用程序，并尝试为 Azure AD 租户启用域服务。* |[Microsoft Graph 应用程序已在 Azure AD 租户中禁用](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>域名冲突

**错误消息**

*名称 aaddscontoso.com 已在此网络中使用。指定一个未使用的名称。*

**解决方法**

确认在同一虚拟网络或对等互连的虚拟网络上没有域名相同的现有 AD DS 环境。 例如，你可能有一个在 Azure VM 上运行的名为 *aaddscontoso.com* 的 AD DS 域。 尝试在虚拟网络上启用具有相同域名（即 aaddscontoso.com）的 Azure AD DS 托管域时，所请求的操作将失败。

发生这种失败的原因是该名称与虚拟网络上的域名冲突。 DNS 查找会检查现有 AD DS 环境是否响应所请求的域名。 若要解决此失败，请使用其他名称设置托管域，或取消预配现有 AD DS 域，然后再次尝试启用 Azure AD DS。

### <a name="inadequate-permissions"></a>权限不足

**错误消息**

*无法在此 Azure AD 租户中启用域服务。该服务对名为“Azure AD 域服务同步”的应用程序没有足够的权限。请删除名为“Azure AD 域服务同步”的应用程序，并尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查你的 Azure AD 目录中是否存在名为“Azure AD 域服务同步”的应用程序。 如果此应用程序存在，请将其删除，然后再次尝试启用 Azure AD DS。 若要检查是否存在现有的应用程序并根据需要将其删除，请完成以下步骤：

1. 在 Azure 门户的左侧导航菜单中，选择“Azure Active Directory”。
1. 选择“企业应用程序”。 在“应用程序类型”下拉菜单中，选择“所有应用程序”，然后选择“应用” 。
1. 在搜索框中输入“Azure AD 域服务同步”。如果此应用程序存在，请选择它并选择“删除”。
1. 删除此应用程序后，再次尝试启用 Azure AD DS。

### <a name="invalid-configuration"></a>配置无效

**错误消息**

*无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有所需的权限来启用域服务。请删除标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的应用程序，并尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查你的 Azure AD 目录中是否存在名为“AzureActiveDirectoryDomainControllerServices”且应用程序标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的现有应用程序。  如果此应用程序存在，请将其删除，然后再次尝试启用 Azure AD DS。

使用以下 PowerShell 脚本搜索现有的应用程序实例，并根据需要将其删除：

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

*无法在此 Azure AD 租户中启用域服务。Microsoft Azure AD 应用程序已在 Azure AD 租户中禁用。请启用标识符为 00000002-0000-0000-c000-000000000000 的应用程序，并尝试为 Azure AD 租户启用域服务。*

**解决方法**

检查是否已禁用标识符为 00000002-0000-0000-c000-000000000000 的应用程序。 此应用程序是一个 Microsoft Azure AD 应用程序，为图形 API 提供对 Azure AD 租户的访问权限。 若要同步 Azure AD 租户，必须启用此应用程序。

若要检查此应用程序的状态并根据需要将其启用，请完成以下步骤：

1. 在 Azure 门户的左侧导航菜单中，选择“Azure Active Directory”。
1. 选择“企业应用程序”。 在“应用程序类型”下拉菜单中，选择“所有应用程序”，然后选择“应用” 。
1. 在搜索框中输入 00000002-0000-0000-c000-00000000000。 选择此应用程序，然后选择“属性”。
1. 如果“为要登录的用户启用”设置为“否”，请将值设置为“是”，然后选择“保存”。
1. 启用此应用程序后，再次尝试启用 Azure AD DS。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>用户无法登录 Azure AD 域服务托管域

如果你的 Azure AD 租户中有一个或多个用户无法登录到托管域，请完成以下故障排除步骤：

* **凭据格式** - 请尝试使用 UPN 格式来指定凭据，例如 `dee@aaddscontoso.onmicrosoft.com`。 UPN 格式是在 Azure AD DS 中指定凭据时建议使用的方法。 请确保在 Azure AD 中正确配置了此 UPN。

    如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，系统可能会自动生成帐户的 SAMAccountName，例如 AADDSCONTOSO\driley。 因此，帐户的 SAMAccountName 格式可能不同于你在本地域中所需的格式或使用的格式。

* **密码同步** - 请确保已为[纯云用户][cloud-only-passwords]或[使用 Azure AD Connect 的混合环境][hybrid-phs]启用了密码同步。
    * **混合同步帐户：** 如果受影响的用户帐户是从本地目录同步而来的，请验证以下方面：
    
      * 是否已部署或更新到[建议使用的最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。
      * 是否已将 Azure AD Connect 配置为[执行完全同步][hybrid-phs]。
      * 根据目录的大小，可能需要一段时间之后才可在托管域中使用用户帐户和凭据哈希。 在尝试向托管域进行身份验证之前，请确保等待足够长的时间。
      * 如果在验证上述步骤后问题仍然出现，请尝试重启“Microsoft Azure AD 同步服务”。 在 Azure AD Connect 服务器上，打开命令提示符，并运行以下命令：
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **仅限云帐户**：如果受影响的用户帐户是纯云用户帐户，请确保[用户在你启用 Azure AD DS 后已更改其密码][cloud-only-passwords]。 此密码重置操作将生成托管域所需的凭据哈希。

* **验证用户帐户是否处于活动状态**：默认情况下，在托管域上于 2 分钟内进行五次无效密码尝试会导致用户帐户被锁定 30 分钟。 当帐户被锁定时，用户无法登录。30 分钟后用户帐户将自动解锁。
  * 在托管域上尝试无效密码不会在 Azure AD 中锁定用户帐户， 只会在托管域中锁定用户帐户。 请使用[管理 VM][management-vm] 检查 Active Directory 管理控制台 (ADAC) 中的用户帐户状态，而不是在 Azure AD 中这样做。
  * 还可以[配置细化密码策略][password-policy]来更改默认的锁定阈值和持续时间。

* **外部帐户** - 确保受影响的用户帐户不是 Azure AD 租户中的外部帐户。 外部帐户示例包括 Microsoft 帐户（例如 `dee@live.com`），或来自外部 Azure AD 目录的用户帐户。 Azure AD DS 不存储外部用户帐户的凭据，因此此类帐户无法登录到托管域。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>托管域上出现一个或多个警报

如果托管域上存在处于活动状态的警报，可能会阻止身份验证过程正常运行。

若要查看是否有任何处于活动状态的警报，请[检查托管域的运行状况][check-health]。 如果显示了任何警报，请[排查并解决它们][troubleshoot-alerts]。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>从 Azure AD 租户中删除的用户不会从托管域中删除

Azure AD 会防止意外删除用户对象。 如果从 Azure AD 租户中删除某个用户帐户，相应的用户对象将移到回收站。 将此删除操作同步到托管域时，会导致相应的用户帐户被标记为已禁用。 可以借助此功能来恢复或取消删除用户帐户。

即使在 Azure AD 目录中重新创建带相同 UPN 的用户帐户，托管域中的用户帐户也仍然会处于禁用状态。 若要从托管域中删除用户帐户，需要从 Azure AD 租户中强制删除该帐户。

若要从托管域中完全删除某个用户帐户，请使用带 `-RemoveFromRecycleBin` 参数的 [Remove-MsolUser][Remove-MsolUser] PowerShell cmdlet 从 Azure AD 租户中永久删除该用户。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[提交 Azure 支持请求][azure-support]以获取更多的故障排除帮助。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
