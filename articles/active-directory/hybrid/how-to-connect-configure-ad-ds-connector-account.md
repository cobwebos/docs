---
title: Azure AD Connect：配置 AD DS 连接器帐户权限 | Microsoft Docs
description: 本文档详细介绍了如何使用新的 ADSyncConfig PowerShell 模块 AD DS 连接器帐户
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff151ff8e14b5cf9602d4e7e2e9c6cb2118a8a65
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918509"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect： 配置 AD DS 连接器帐户权限 

内部版本 1.1.880.0（发布于 2018 年 8 月）中引入了名为 [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) 的 PowerShell 模块，其中包括有助于为 Azure AD Connect 部署配置正确 Active Directory 权限的 cmdlet 集合。 

## <a name="overview"></a>概述 
对于选择要在 Azure AD Connect 中启用的每个功能，可以使用以下 PowerShell cmdlet 设置 AD DS Connector 帐户的 Active Directory 权限。 为了防止出现任何问题，每当要使用自定义域帐户安装 Azure AD Connect 以连接林时，都应提前准备 Active Directory 权限。 部署 Azure AD Connect 后，此 ADSyncConfig 模块还可用于配置权限。

![ad ds 帐户的概述](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

对于 Azure AD Connect 快速安装，将在 Active Directory 中创建一个具有所有必需权限的自动生成的帐户 (MSOL_nnnnnnnnnn)，因此除非你已阻止对组织单位或要同步到 Azure AD 的特定 Active Directory 对象的权限继承，否则无需使用此 ADSyncConfig 模块。 
 
### <a name="permissions-summary"></a>权限摘要 
下表提供了 AD 对象所需权限的摘要： 

| Feature | 权限 |
| --- | --- |
| ms DS ConsistencyGuid 功能 |对[设计概念 - 使用 ms-DS-ConsistencyGuid 作为 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 中所述的 ms-DS-ConsistencyGuid 属性的写入权限。 | 
| 密码哈希同步 |<li>复制目录更改</li>  <li>复制所有目录更改 |
| Exchange 混合部署 |针对用户、组和联系人的属性的写入权限，详见[Exchange 混合写回](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)。 |
| Exchange 邮件公共文件夹 |对 [Exchange 邮件公用文件夹](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)中所述的公用文件夹属性的读取权限。 | 
| 密码写回 |针对用户的属性的写入权限，详见[密码管理入门](../authentication/howto-sspr-writeback.md)。 |
| 设备写回 |写入[设备写回](how-to-connect-device-writeback.md)中记录的设备对象和容器的权限。 |
| 组写回 |读取、创建、更新和删除同步的 **Office 365 组**的组对象。  有关详细信息，请参阅[组写回](how-to-connect-preview.md#group-writeback)。|

## <a name="using-the-adsyncconfig-powershell-module"></a>使用 ADSyncConfig PowerShell 模块 
ADSyncConfig 模块需要[适用于 AD DS 的远程服务器管理工具 (RSAT)](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools)，因为它依赖于 AD DS PowerShell 模块和工具。 若要安装适用于 AD DS 的 RSAT，请使用“以管理员身份运行”打开 Windows PowerShell 窗口并执行： 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![配置](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>也可以将文件 **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** 复制到已安装了“适用于 AD DS 的 RSAT”的域控制器，并从该控制器使用此 PowerShell 模块。

若要开始使用 ADSyncConfig，则需要在 Windows PowerShell 窗口中加载该模块： 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

若要检查此模块中包含的所有 cmdlet，可以键入：  

``` powershell
Get-Command -Module AdSyncConfig  
```

![勾选标记](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

每个 cmdlet 都具有相同的参数来输入 AD DS 连接器帐户和 AdminSDHolder 开关。 若要指定 AD DS 连接器帐户，可以提供帐户名称和域，或仅提供帐户可分辨名称 (DN)，

例如：

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

或；

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

请务必替换`<ADAccountName>`，`<ADDomainName>`和`<ADAccountDN>`替换为你的环境的正确值。

如果不想修改 AdminSDHolder 容器的权限，请使用开关 `-SkipAdminSdHolders`。 

默认情况下，所有设定的权限 cmdlet 都将尝试在林中每个域的根上设置 AD DS 权限，这意味着运行 PowerShell 会话的用户需要林中每个域的域管理员权限。  由于此要求，建议从林根中使用企业管理员。 如果 Azure AD Connect 部署具有多个 AD DS 连接器，则需要在具有 AD DS 连接器的每个林上运行相同的 cmdlet。 

还可以使用参数 `-ADobjectDN`（后跟要设置权限的目标对象的 DN）来设置特定 OU 或 AD DS 对象的权限。 使用目标 ADobjectDN 时，cmdlet 将仅对此对象设置权限，而不对域根或 AdminSDHolder 容器设置权限。 若所拥有的特定 OU 或 AD DS 对象已禁用权限继承，此参数非常有用（请参阅“查找已禁用权限继承的 AD DS 对象”） 

这些常用参数的例外情况是用于对 AD DS 连接器帐户本身设置权限的 `Set-ADSyncRestrictedPermissions` cmdlet 以及 `Set-ADSyncPasswordHashSyncPermissions` cmdlet，因为密码哈希同步所需的权限仅在域根中进行了设置，所以此 cmdlet 不包含 `-ObjectDN` 或 `-SkipAdminSdHolders` 参数。

### <a name="determine-your-ad-ds-connector-account"></a>确定 AD DS 连接器帐户 
如果已安装 Azure AD Connect 并且想要检查 Azure AD Connect 当前正在使用的 AD DS 连接器帐户，则可以执行 cmdlet： 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>查找已禁用权限继承的 AD DS 对象 
如果想要检查是否存在任何已禁用权限继承的 AD DS 对象，可以运行： 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
默认情况下，此 cmdlet 将仅查找已禁用继承的 OU，但你可以在 `-ObjectClass` 参数中指定其他 AD DS 对象类或使用“*”查找所有对象类，如下所示： 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>查看对象的 AD DS 权限 
可以使用以下 cmdlet 查看当前 Active Directory 对象上设置通过提供其可分辨名称的权限的列表： 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>配置 AD DS 连接器帐户权限 
 
### <a name="configure-basic-read-only-permissions"></a>配置基础只读权限 
若要在不使用任何 Azure AD Connect 功能时为 AD DS 连接器帐户设置基础只读权限，请运行： 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或； 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


此 cmdlet 将设置以下权限： 
 

|Type |姓名 |访问 |应用于| 
|-----|-----|-----|-----|
|允许 |AD DS 连接器帐户 |读取所有属性 |后代设备对象| 
|允许 |AD DS 连接器帐户|读取所有属性 |后代 InetOrgPerson 对象| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代计算机对象| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代 foreignSecurityPrincipal 对象| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代组对象| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代用户对象| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代联系人对象| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>配置 MS-DS-Consistency-Guid 权限 
若要在使用 ms-Ds-Consistency-Guid 属性作为源定位点（也就是“让 Azure 为我管理源定位点”选项）时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

或； 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许|AD DS 连接器帐户|读取/写入属性|后代用户对象|

### <a name="permissions-for-password-hash-synchronization"></a>密码哈希同步的权限 
若要在使用密码哈希同步时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


或； 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |AD DS 连接器帐户 |复制目录更改 |仅限此对象（域根）| 
|允许 |AD DS 连接器帐户 |复制所有目录更改 |仅限此对象（域根）| 
  
### <a name="permissions-for-password-writeback"></a>密码写回的权限 
若要在使用密码写回时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或；

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |AD DS 连接器帐户 |重置密码 |后代用户对象| 
|允许 |AD DS 连接器帐户 |写入 lockoutTime 属性 |后代用户对象| 
|允许 |AD DS 连接器帐户 |写入 pwdLastSet 属性 |后代用户对象| 

### <a name="permissions-for-group-writeback"></a>组写回的权限 
若要在使用组写回时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
或； 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |AD DS 连接器帐户 |一般读取/写入 |对象类型组和子对象的所有属性| 
|允许 |AD DS 连接器帐户 |创建/删除子对象 |对象类型组和子对象的所有属性| 
|允许 |AD DS 连接器帐户 |删除/删除树对象|对象类型组和子对象的所有属性|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange 混合部署的权限 
若要在使用 Exchange 混合部署时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或； 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

此 cmdlet 将设置以下权限：  
 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |AD DS 连接器帐户 |读取/写入所有用户属性 |后代用户对象| 
|允许 |AD DS 连接器帐户 |读取/写入所有用户属性 |后代 InetOrgPerson 对象| 
|允许 |AD DS 连接器帐户 |读取/写入所有用户属性 |后代组对象| 
|允许 |AD DS 连接器帐户 |读取/写入所有用户属性 |后代联系人对象| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange 邮件公用文件夹（预览版）的权限 
若要在使用 Exchange 邮件公用文件夹功能时为 AD DS 连接器帐户设置权限，请运行： 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


或； 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |AD DS 连接器帐户 |读取所有属性 |后代 PublicFolder 对象| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>限制 AD DS 连接器帐户的权限 
此 PowerShell 脚本将限制以参数提供的 AD 连接器帐户的权限。 限制权限操作包括以下步骤： 

- 禁用指定对象上的继承 
- 删除特定对象上的所有 ACE（特定于 SELF 的 ACE 除外），因为对于 SELF，我们希望默认权限保持不变。 
 
  -ADConnectorAccountDN 参数是需要限制权限的 AD 帐户。 这通常是在 AD DS 连接器中配置的 MSOL_nnnnnnnnnnnn 域帐户（请参阅“确定 AD DS 连接器帐户”）。 若要指定具有必要权限以限制目标 AD 对象的 Active Directory 权限的管理员帐户，则必需使用 -Credential 参数。 这通常为企业或域管理员。  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
例如： 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

此 cmdlet 将设置以下权限： 

|Type |姓名 |访问 |应用于|
|-----|-----|-----|-----| 
|允许 |SYSTEM |完全控制 |此对象 
|允许 |企业管理员 |完全控制 |此对象 
|允许 |域管理员 |完全控制 |此对象 
|允许 |管理员 |完全控制 |此对象 
|允许 |企业域控制器 |列出内容 |此对象 
|允许 |企业域控制器 |读取所有属性 |此对象 
|允许 |企业域控制器 |读取权限 |此对象 
|允许 |经过身份验证的用户 |列出内容 |此对象 
|允许 |经过身份验证的用户 |读取所有属性 |此对象 
|允许 |经过身份验证的用户 |读取权限 |此对象 

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect：帐户和权限](reference-connect-accounts-permissions.md)
- [快速安装](how-to-connect-install-express.md)
- [自定义安装](how-to-connect-install-custom.md)
- [ADSyncConfig 参考](reference-connect-adsyncconfig.md)

