---
title: Azure AD Connect：ADSyncConfig PowerShell 参考 | Microsoft Docs
description: 本文档提供 ADSyncConfig.psm1 PowerShell 模块的参考信息。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8159ef45dee8a2f9ace69c2a5b66a29e4948d82c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981997"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect：ADSyncConfig PowerShell 参考
以下文档提供了 Azure AD Connect 附带的 ADSyncConfig.psm1 PowerShell 模块的参考信息。


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>摘要
获取在每个 AD Connector 中配置的帐户名和域

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>说明
此函数使用 AAD Connect 中显示的“Get-ADSyncConnector”cmdlet 从连接参数中检索显示 AD Connector 帐户的表。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>摘要
获得已禁用权限继承的 AD 对象

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>说明
从 SearchBase 参数开始在 AD 中搜索并返回按 ObjectClass 参数筛选的所有对象，这些对象具有当前禁用的 ACL 继承。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
使用 "Contoso" 域中的已禁用继承查找对象（默认情况下，仅返回 "organizationalUnit" 对象）
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>示例 2
在 "Contoso" 域中查找 "用户" 对象并禁用继承
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>示例 3
在 OU 中查找具有禁用的继承的所有类型的对象
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETERS

#### <a name="-searchbase"></a>-SearchBase
可以是 AD 域 DistinguishedName 或 FQDN 的 LDAP 查询的 SearchBase

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
要搜索的对象类，可以是“*”（适用于任何对象类）、“user”、“group”、“container”等。默认情况下，此函数将搜索“organizationalUnit”对象类。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以获取基本读取权限。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncBasicReadPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
读取所有后代计算机对象的所有属性的属性访问权限
2.
读取所有后代设备对象的所有属性的属性访问权限
3.
读取所有后代 foreignsecurityprincipal 对象的所有属性的属性访问权限
5.
读取所有后代用户对象的所有属性的属性访问权限
6.
读取所有后代 inetorgperson 对象的所有属性的属性访问权限
7.
读取所有后代组对象的所有属性的属性访问权限
8.
读取所有后代联系人对象的所有属性的属性访问权限

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以获取 Exchange 混合功能。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncExchangeHybridPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
读取/写入所有后代用户对象的所有属性的属性访问权限
2.
读取/写入所有后代 inetorgperson 对象的所有属性的属性访问权限
3.
读取/写入所有后代组对象的所有属性的属性访问权限
4.
读取/写入所有后代联系人对象的所有属性的属性访问权限

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以获取 Exchange 邮件公用文件夹。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncExchangeMailPublicFolderPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
读取所有后代 publicfolder 对象的所有属性的属性访问权限

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以获取 mS-DS-ConsistencyGuid 功能。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncMsDsConsistencyGuidPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
读取/写入所有后代用户对象的 mS-DS-ConsistencyGuid 属性的属性访问权限

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以获取密码哈希同步。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncPasswordHashSyncPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
复制目录更改
2.
复制所有目录更改

这些权限将授予给林中的所有域。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
将被 Azure AD Connect 同步用来管理目录中的对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
将被 Azure AD Connect 同步用来管理目录中的对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
将被 Azure AD Connect 同步用来管理目录中的对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以从 Azure AD 进行密码回写。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncPasswordWritebackPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
重置后代用户对象的密码
2.
写入所有后代用户对象的 lockoutTime 属性的属性访问权限
3.
写入所有后代用户对象的 pwdLastSet 属性的属性访问权限

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>摘要
加强 AD 对象的权限，否则该对象不包含在任何受 AD 保护的安全组中。
典型示例是由 AAD Connect 自动创建的 AD Connect 帐户 (MSOL)。
此帐户具有所有域的复制权限，但由于不受保护，因此很容易受到入侵。

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
Set-ADSyncRestrictedPermissions 函数将增强所提供帐户的权限。
限制权限操作包括以下步骤：
1.
禁用指定对象上的继承
2.
删除特定对象上的所有 ACE，但特定于 SELF 的 ACE 除外。
我们希望在处理 SELF 时默认权限保持不变。
3.
分配以下特定权限：

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
需要加强其权限的 Active Directory 帐户的 DistinguishedName。
这通常是 MSOL_nnnnnnnnnn 帐户或 AD Connector 中配置的自定义域帐户。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
管理员凭据，拥有限制 ADConnectorAccountDN 帐户权限的必要特权。 这通常是企业管理员或域管理员。 使用管理员帐户的完全限定域名来避免帐户查找失败。
示例：CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
使用 DisableCredentialValidation 时，该函数不会检查 -Credential 中提供的凭据在 AD 中是否有效以及所提供的帐户是否具有必要的特权来限制 ADConnectorAccountDN 帐户权限。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示运行该 cmdlet 时会发生什么情况。
cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216) 。

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>摘要
初始化 Active Directory 林和域以从 Azure AD 进行组回写。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Set-ADSyncUnifiedGroupWritebackPermissions 函数将为 AD 同步帐户提供所需的权限，其中包括以下内容：
1.
针对所有组对象类型和子对象的一般读取/写入、删除、删除树和创建\删除子级

这些权限适用于林中的所有域。
（可选）可在 ADobjectDN 参数中提供 DistinguishedName，以仅在该 AD 对象上设置这些权限（包括对子对象的继承）。
在这种情况下，ADobjectDN 将是与 GroupWriteback 功能链接的所需容器的专有名称。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>示例 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>示例 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>示例 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的名称。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的域。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Azure AD Connect 同步现在或将来用其管理目录对象的 Active Directory 帐户的 DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
用于设置权限的目标 AD 对象的 DistinguishedName（可选）

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
可选参数，指示是否不应使用这些权限更新 AdminSDHolder 容器

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
显示在此 cmdlet 运行的情况下将会发生什么。
此 cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>摘要
显示指定 AD 对象的权限。

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
此函数返回当前为参数 -ADobjectDN 中提供的给定 AD 对象设置的所有 AD 权限。
ADobjectDN 必须以 DistinguishedName 格式提供。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{填写 ADobjectDN 说明}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。
