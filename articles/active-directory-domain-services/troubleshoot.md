---
title: Azure 活动目录域服务故障排除 |微软文档
description: 了解如何在创建或管理 Azure 活动目录域服务时解决常见错误
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
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365809"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure 活动目录域服务的常见错误和故障排除步骤

作为应用程序标识和身份验证的核心部分，Azure 活动目录域服务 （Azure AD DS） 有时存在问题。 如果遇到问题，则有一些常见的错误消息和相关故障排除步骤，以帮助您重新运行某些操作。 您可以随时[打开 Azure 支持请求][azure-support]，以获取其他故障排除帮助。

本文提供了 Azure AD DS 中常见问题的故障排除步骤。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>无法为 Azure AD 目录启用 Azure AD 域服务

如果在启用 Azure AD DS 时遇到问题，请查看以下常见错误和解决这些问题的步骤：

| **示例错误消息** | **分辨率** |
| --- |:--- |
| *此网络上已使用aaddscontoso.com的名称。指定不使用的名称。* |[虚拟网络中的域名冲突](troubleshoot.md#domain-name-conflict) |
| *无法在此 Azure AD 租户中启用域服务。该服务对称为"Azure AD 域服务同步"的应用程序没有适当的权限。删除名为"Azure AD 域服务同步"的应用程序，然后尝试为 Azure AD 租户启用域服务。* |[域服务对 Azure AD 域服务同步应用程序没有足够的权限](troubleshoot.md#inadequate-permissions) |
| *无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有启用域服务所需的权限。使用应用程序标识符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 删除应用程序，然后尝试为 Azure AD 租户启用域服务。* |[域服务应用程序未在 Azure AD 租户中正确配置](troubleshoot.md#invalid-configuration) |
| *无法在此 Azure AD 租户中启用域服务。在 Azure AD 租户中禁用 Microsoft Azure AD 应用程序。使用应用程序标识符 0000000-0000-0000-c000-0000000000 启用应用程序，然后尝试为 Azure AD 租户启用域服务。* |[Microsoft Graph 应用程序已在 Azure AD 租户中禁用](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>域名冲突

**错误消息**

*此网络上已使用aaddscontoso.com的名称。指定不使用的名称。*

**分辨率**

检查您没有具有相同域名的现有 AD DS 环境，或者对等虚拟网络。 例如，您可能有一个名为*aaddscontoso.com*的 AD DS 域在 Azure VM 上运行。 当您尝试在虚拟网络上启用具有相同*aaddscontoso.com*域名的 Azure AD DS 托管域时，请求的操作将失败。

此失败是由于虚拟网络上的域名的名称冲突造成的。 DNS 查找检查现有 AD DS 环境是否响应请求的域名。 要解决此故障，请使用其他名称设置 Azure AD DS 托管域，或取消预配现有的 AD DS 域，然后重试启用 Azure AD DS。

### <a name="inadequate-permissions"></a>权限不足

**错误消息**

*无法在此 Azure AD 租户中启用域服务。该服务对称为"Azure AD 域服务同步"的应用程序没有适当的权限。删除名为"Azure AD 域服务同步"的应用程序，然后尝试为 Azure AD 租户启用域服务。*

**分辨率**

检查 Azure AD 目录中是否有名为*Azure AD 域服务同步*的应用程序。 如果此应用程序存在，请将其删除，然后重试以启用 Azure AD DS。 要检查现有应用程序并在需要时将其删除，请完成以下步骤：

1. 在 Azure 门户中，从左侧导航菜单中选择**Azure 活动目录**。
1. 选择“企业应用程序”。**** 从 **"应用程序类型"** 下拉菜单中选择*所有应用程序*，然后选择 **"应用**"。
1. 在搜索框中，输入*Azure AD 域服务同步*。如果应用程序存在，请选择它并选择 **"删除**"。
1. 删除应用程序后，请尝试再次启用 Azure AD DS。

### <a name="invalid-configuration"></a>配置无效

**错误消息**

*无法在此 Azure AD 租户中启用域服务。Azure AD 租户中的域服务应用程序没有启用域服务所需的权限。使用应用程序标识符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 删除应用程序，然后尝试为 Azure AD 租户启用域服务。*

**分辨率**

检查 Azure AD 目录中是否有名为*AzureActiveDirectoryDomainController 服务*的现有应用程序，应用程序标识符*为 d87dcbc6-a371-462e-88e3-28ad15ec4e64。* 如果此应用程序存在，请将其删除，然后重试以启用 Azure AD DS。

使用以下 PowerShell 脚本搜索现有应用程序实例，并在需要时将其删除：

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

*无法在此 Azure AD 租户中启用域服务。在 Azure AD 租户中禁用 Microsoft Azure AD 应用程序。使用应用程序标识符 0000000-0000-0000-c000-0000000000 启用应用程序，然后尝试为 Azure AD 租户启用域服务。*

**分辨率**

检查是否禁用了具有标识符 0000000-0000-c000-00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 *00000002-0000-0000-c000-000000000000* 此应用程序是一个 Microsoft Azure AD 应用程序，为图形 API 提供对 Azure AD 租户的访问权限。 要同步 Azure AD 租户，必须启用此应用程序。

要检查此应用程序的状态并在需要时启用它，请完成以下步骤：

1. 在 Azure 门户中，从左侧导航菜单中选择**Azure 活动目录**。
1. 选择“企业应用程序”。**** 从 **"应用程序类型"** 下拉菜单中选择*所有应用程序*，然后选择 **"应用**"。
1. 在搜索框中，输入*0000000-0000-0000-c000-0000000000000000000000。* 选择应用程序，然后选择**属性**。
1. 如果**启用用户登录**设置为 *"否*"，则将值设置为 *"是*"，则选择"**保存**"。
1. 启用应用程序后，请尝试再次启用 Azure AD DS。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>用户无法登录 Azure AD 域服务托管域

如果 Azure AD 租户中的一个或多个用户无法登录到 Azure AD DS 托管域，请完成以下故障排除步骤：

* **凭据格式**- 尝试使用 UPN 格式指定凭据，`dee@aaddscontoso.onmicrosoft.com`如 。 UPN 格式是在 Azure AD DS 中指定凭据的推荐方式。 确保此 UPN 在 Azure AD 中配置正确。

    如果租户中有多个具有相同 UPN 前缀的用户，或者您的 UPN 前缀过长，则自动生成帐户*名称*，例如*AADDSCONTOSO_driley。* 因此，您帐户的*SAMAccountName*格式可能与您期望或在本地域中使用的内容不同。

* **密码同步**- 确保已为[仅云用户][cloud-only-passwords]或使用 Azure AD Connect 为[混合环境][hybrid-phs]启用密码同步。
    * **混合同步帐户：** 如果受影响的用户帐户从本地目录同步，请验证以下区域：
    
      * 您已部署或更新到[Azure AD 连接的最新推荐版本](https://www.microsoft.com/download/details.aspx?id=47594)。
      * 已配置 Azure AD 连接以[执行完全同步][hybrid-phs]。
      * 根据目录的大小，用户帐户和凭据哈希可能需要一段时间才能在 Azure AD DS 中可用。 在尝试对托管域进行身份验证之前，请确保等待足够长的时间。
      * 如果在验证前面的步骤后问题仍然存在，请尝试重新启动*Microsoft Azure AD 同步服务*。 从 Azure AD 连接服务器打开命令提示并运行以下命令：
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **仅限云帐户**：如果受影响的用户帐户是仅云用户帐户，请确保[用户在启用 Azure AD DS 后更改了密码][cloud-only-passwords]。 此密码重置会导致生成 Azure AD 域服务所需的凭据哈希。

* **验证用户帐户处于活动状态**：默认情况下，在托管域上 2 分钟内的 5 次无效密码尝试会导致用户帐户被锁定 30 分钟。 当帐户被锁定时，用户无法登录。30 分钟后，用户帐户将自动解锁。
  * Azure AD DS 托管域上的无效密码尝试不会锁定 Azure AD 中的用户帐户。 用户帐户仅在托管域中锁定。 使用[管理 VM（][management-vm]不在 Azure AD*中）检查活动目录管理控制台 （ADAC）* 中的用户帐户状态。
  * 您还可以[配置细粒度密码策略][password-policy]以更改默认锁定阈值和持续时间。

* **外部帐户**- 检查受影响的用户帐户不是 Azure AD 租户中的外部帐户。 外部帐户的示例包括 Microsoft 帐户`dee@live.com`（如）或来自外部 Azure AD 目录的用户帐户。 Azure AD DS 不存储外部用户帐户的凭据，因此无法登录到托管域。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>托管域上出现一个或多个警报

如果 Azure AD DS 托管域上有活动警报，则可能会阻止身份验证过程正常工作。

要查看是否有任何活动警报，[请检查 Azure AD DS 托管域的运行状况][check-health]状态。 如果显示任何警报，[则排除故障并解决它们][troubleshoot-alerts]。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>从 Azure AD 租户中删除的用户不会从托管域中删除

Azure AD 可防止意外删除用户对象。 从 Azure AD 租户中删除用户帐户时，相应的用户对象将移动到回收站。 当此删除操作同步到 Azure AD DS 托管域时，相应的用户帐户将标记为已禁用。 此功能可帮助您恢复或取消删除用户帐户。

用户帐户在 Azure AD DS 托管域中保持禁用状态，即使您在 Azure AD 目录中重新创建具有相同 UPN 的用户帐户也是如此。 要从 Azure AD DS 托管域中删除用户帐户，需要强制将其从 Azure AD 租户中删除。

要从 Azure AD DS 托管域中完全删除用户帐户，请使用带有`-RemoveFromRecycleBin`该参数[的"删除-MsolUser][Remove-MsolUser] PowerShell cmdlet"永久删除 Azure AD 租户中的用户帐户。

## <a name="next-steps"></a>后续步骤

如果仍然存在问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
