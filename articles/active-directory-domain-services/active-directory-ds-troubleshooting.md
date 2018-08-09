---
title: Azure Active Directory 域服务：故障排除指南 | Microsoft 文档
description: Azure AD 域服务故障排除指南
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: maheshu
ms.openlocfilehash: 41a06846e914eb79e15aa47c7203b17166731ae0
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502322"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD 域服务 - 故障排除指南
本文提供设置或管理 Azure Active Directory (AD) 域服务时可能遇到的问题的故障排除提示。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>无法为 Azure AD 目录启用 Azure AD 域服务
如果尝试为目录启用 Azure AD 域服务，本部分可帮助排查错误。

选择对应于所遇到的错误消息的故障排除步骤。

| **错误消息** | **解决方法** |
| --- |:--- |
| *名称 contoso100.com 已在此网络中使用。指定一个未使用的名称。* |[虚拟网络中的域名冲突](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *无法在此 Azure AD 租户中启用域服务。该服务对名为“Azure AD 域服务同步”的应用程序没有足够的权限。请删除名为“Azure AD 域服务同步”的应用程序，并尝试为 Azure AD 租户启用域服务。* |[域服务对 Azure AD 域服务同步应用程序没有足够的权限](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有所需的权限来启用域服务。请删除标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的应用程序，并尝试为 Azure AD 租户启用域服务。* |[未在租户中正确配置域服务应用程序](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *无法在此 Azure AD 租户中启用域服务。Microsoft Azure AD 应用程序已在 Azure AD 租户中禁用。请启用标识符为 00000002-0000-0000-c000-000000000000 的应用程序，并尝试为 Azure AD 租户启用域服务。* |[Microsoft Graph 应用程序已在 Azure AD 租户中禁用](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>域名冲突
**错误消息：**

*名称 contoso100.com 已在此网络中使用。指定一个未使用的名称。*

**补救方法：**

确保现有域的名称与该虚拟网络上使用的域名不同。 例如，假设名为“contoso.com”的域已在选定的虚拟网络上使用。 然后，尝试在该虚拟网络上启用具有相同域名（即“contoso.com”）的 Azure AD 域服务托管域。 尝试启用 Azure AD 域服务会失败。

发生这种失败的原因是该名称与该虚拟网络上的域名冲突。 在此情况下，必须使用不同的名称来设置 Azure AD 域服务托管域。 或者，可以取消预配现有的域，并继续启用 Azure AD 域服务。

### <a name="inadequate-permissions"></a>权限不足
**错误消息：**

*无法在此 Azure AD 租户中启用域服务。该服务对名为“Azure AD 域服务同步”的应用程序没有足够的权限。请删除名为“Azure AD 域服务同步”的应用程序，并尝试为 Azure AD 租户启用域服务。*

**补救方法：**

检查 Azure AD 目录中是否存在名为“Azure AD 域服务同步”的应用程序。 如果存在，请将它删除，然后再次启用 Azure AD 域服务。

执行以下步骤，检查是否存在该应用程序，如果存在，则将它删除：

1. 导航至 [Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)中 Azure AD 目录的“应用程序”部分。
2. 选择“显示”下拉列表中的“所有应用程序”。 选择“应用程序状态”下拉列表中的“所有”。 选择“应用程序可见性”下拉列表中的“所有”。
3. 在搜索框中键入“Azure AD 域服务同步”。 如果该应用程序存在，单击它，然后单击工具栏中的“删除”按钮，将其删除。
4. 删除该应用程序后，再次尝试启用 Azure AD 域服务。

### <a name="invalid-configuration"></a>配置无效
**错误消息：**

*无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有所需的权限来启用域服务。请删除标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64 的应用程序，并尝试为 Azure AD 租户启用域服务。*

**补救方法：**

检查 Azure AD 目录中是否存在名为“AzureActiveDirectoryDomainControllerServices”（应用程序标识符为 d87dcbc6-a371-462e-88e3-28ad15ec4e64）的应用程序。 如果存在，需将它删除，然后再次启用 Azure AD 域服务。

使用以下 PowerShell 脚本查找该应用程序并将它删除。

> [!NOTE]
> 此脚本使用 **Azure AD PowerShell 版本 2** cmdlet。 有关所有可用 cmdlet 的完整列表以及如何下载该模块，请阅读 [AzureAD PowerShell 参考文档](https://msdn.microsoft.com/library/azure/mt757189.aspx)。
>
>

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
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 已禁用
**错误消息：**

无法在此 Azure AD 租户中启用域服务。 Microsoft Azure AD 应用程序已在 Azure AD 租户中禁用。 请启用标识符为 00000002-0000-0000-c000-000000000000 的应用程序，并尝试为 Azure AD 租户启用域服务。

**补救方法：**

检查是否已禁用标识符为 00000002-0000-0000-c000-000000000000 的应用程序。 此应用程序是一个 Microsoft Azure AD 应用程序，为图形 API 提供对 Azure AD 租户的访问权限。 需要为 Azure AD 域服务启用此应用程序，才能将 Azure AD 租户同步到托管域。

要解决此错误，请启用此应用程序，并尝试为 Azure AD 租户启用域服务。


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>用户无法登录 Azure AD 域服务托管域
如果 Azure AD 租户中有一个或多个用户无法登录到新建的托管域，请执行以下故障排除步骤：

* **使用 UPN 格式登录：** 尝试使用 UPN 格式（例如“joeuser@contoso.com”）而非 SAMAccountName 格式（例如“CONTOSO\joeuser”）登录。 对于 UPN 前缀过长或与托管域上另一用户相同的用户，系统可能会自动生成 SAMAccountName。 UPN 格式可保证在 Azure AD 租户中唯一。

> [!NOTE]
> 建议使用 UPN 格式登录到 Azure AD 域服务托管域。
>
>

* 确保根据《入门指南》中所述的步骤[启用密码同步](active-directory-ds-getting-started-password-sync.md)。
* **外部帐户：** 确保受影响的用户帐户不是 Azure AD 租户中的外部帐户。 外部帐户的示例包括 Microsoft 帐户（例如“joe@live.com”），或来自外部 Azure AD 目录的用户帐户。 由于 Azure AD 域服务没有此类用户帐户的凭据，因此这些用户无法登录到托管域。
* **已同步的帐户：** 如果受影响的用户帐户已从本地目录同步，请确认：

  * 已部署或更新到[最新版本的 Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594)。
  * 已将 Azure AD Connect 配置为[执行完全同步](active-directory-ds-getting-started-password-sync.md)。
  * 根据目录的大小，可能需要在一段时间后，用户帐户和凭据哈希才可在 Azure AD 域服务中使用。 请确保重试身份验证之前等待足够长的时间。
  * 如果在执行上述步骤后问题仍然出现，请尝试重新启动 Microsoft Azure AD 同步服务。 在同步计算机上启动命令提示符，并执行以下命令：

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **仅限云帐户**：如果受影响的用户帐户是仅限云的用户帐户，请确保在启用 Azure AD 域服务之后，用户已更改其密码。 执行此步骤可以生成 Azure AD 域服务所需的凭据哈希。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>托管域上出现一个或多个警报

有关如何解决托管域上的警报，请访问[排查警报问题](active-directory-ds-troubleshoot-alerts.md)一文。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>从 Azure AD 租户中删除的用户不会从托管域中删除
Azure AD 会防止意外删除用户对象。 如果从 Azure AD 租户中删除某个用户帐户，相应的用户对象将移到回收站。 将此删除操作同步到托管域时，会导致相应的用户帐户标记为已禁用。 今后可以借助此功能来恢复或取消删除用户帐户。

即使在 Azure AD 目录中重新创建带相同 UPN 的用户帐户，托管域中的用户帐户仍处于禁用状态。 若要从托管域中删除用户帐户，需要从 Azure AD 租户中强制删除该帐户。

若要从托管域中完全删除用户帐户，可以从 Azure AD 租户中永久删除该用户。 结合使用 `Remove-MsolUser` PowerShell cmdlet 和 `-RemoveFromRecycleBin` 选项，参照 [MSDN 文章](https://msdn.microsoft.com/library/azure/dn194132.aspx)中的介绍。


## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
