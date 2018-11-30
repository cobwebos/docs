---
title: Azure AD Connect：ADSyncTools PowerShell 参考 | Microsoft Docs
description: 本文档提供 ADSyncTools.psm1 PowerShell 模块的参考信息。
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 586f3c81fad79f49b3506b0bd84a4f7b218d8605
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426479"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect：ADSyncTools PowerShell 参考
以下文档提供了 Azure AD Connect 随附的 ADSyncTools.psm1 PowerShell 模块的参考信息。

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>摘要
清除 AD 用户的 mS-Ds-ConsistencyGuid

### <a name="syntax"></a>语法

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
清除目标 AD 用户的 mS-Ds-ConsistencyGuid 中的值

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-user"></a>-User
要在 AD 中设置的目标用户

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

### <a name="parameters"></a>参数

#### <a name="-database"></a>-Database
{{Fill Database Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Fill Instance Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill Password Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill Server Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill UserName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>摘要
导出 ConsistencyGuid 报告

### <a name="syntax"></a>语法

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>说明
基于 Import-ADSyncToolsImmutableIdMigration 导入的 CSV 文件生成 ConsistencyGuid 报告

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
使用备用登录 ID（邮件）

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
CSV 和日志文件的输出文件名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

### <a name="parameters"></a>参数

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>摘要
从 AD 获取用户

### <a name="syntax"></a>语法

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
返回 AD 对象（TO DO: 多林支持）

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-user"></a>-User
AD 中要设置 ConsistencyGuid AD 的目标用户

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>摘要
从 AD 用户获取 mS-Ds-ConsistencyGuid

### <a name="syntax"></a>语法

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
以 GUID 格式返回目标 AD 用户的 mS-Ds-ConsistencyGuid 属性中的值

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-user"></a>-User
要在 AD 中设置的目标用户

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>摘要
从 AD 用户获取 ObjectGuid

### <a name="syntax"></a>语法

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
以 GUID 格式返回目标 AD 用户的 ObjectGUID 属性中的值

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-user"></a>-User
要在 AD 中设置的目标用户

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>摘要
获取 AAD 连接运行历史记录

### <a name="syntax"></a>语法

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>说明
以 XML 格式返回 AAD 连接运行历史记录的函数

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>示例 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>参数

#### <a name="-days"></a>-Days
{{Fill Days Description}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>摘要
获取发生了 SourceAnchor 更改错误的用户

### <a name="syntax"></a>语法

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
函数将查询 AAD 连接运行历史记录，并导出报告了“SourceAnchor 属性已更改”错误的所有用户。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file path with file name" #"\<Source_Path\>" $outputPath = Read-Host -Prompt "Enter your out file path with file name" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fill outputPath Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>摘要
从 AAD 导入 ImmutableID

### <a name="syntax"></a>语法

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>说明
生成包含所有 Azure AD 同步用户的文件，这些用户具有符合 GUID 格式要求的 ImmutableID 值：MSOnline PowerShell 模块

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-output"></a>-Output
输出 CSV 文件

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
从 Azure AD 回收站获取同步用户

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

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

### <a name="parameters"></a>参数

#### <a name="-query"></a>-Query
{{Fill Query Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection Description}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>摘要
用于从 UserCertificate 属性中删除已过期证书的脚本

### <a name="syntax"></a>语法

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>说明
此脚本采用 Active Directory 域的目标组织单位中的所有对象（已按对象类（用户/计算机）筛选），并删除 UserCertificate 属性中的所有已过期证书。
默认情况下（仅限 BackupOnly 模式），它只会将已过期的证书备份到文件，而不会在 AD 中执行任何更改。
如果使用 -BackupOnly $false，则在将这些对象的 UserCertificate 属性中的任何已过期证书复制到文件后，会从 AD 中删除该证书。
每个证书将备份到单独的文件名：ObjectClass_ObjectGUID_CertThumprint.cer。该脚本还会创建 CSV 格式的日志文件，其中显示证书有效或已过期的所有用户，包括执行的实际操作（已跳过/已导出/已删除）。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>示例 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>参数

#### <a name="-targetou"></a>-TargetOU
要在其中查找 AD 对象的目标 OU

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

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly 不会从 AD 中删除任何证书

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
对象类筛选器

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>摘要
简短说明

### <a name="syntax"></a>语法

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>说明
长说明

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

### <a name="parameters"></a>参数

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>摘要
(TO DO) 从证书文件还原 AD UserCertificate 属性

### <a name="syntax"></a>语法

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>说明
长说明

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>摘要
对 AD 用户设置 mS-Ds-ConsistencyGuid

### <a name="syntax"></a>语法

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>说明
在目标 AD 用户的 mS-Ds-ConsistencyGuid 属性中设置值

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-user"></a>-User
AD 中要设置 ConsistencyGuid AD 的目标用户

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId（字节数组、GUID、GUID 字符串或 Base64 字符串）

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>摘要
{{Fill in the Synopsis}}

### <a name="syntax"></a>语法

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>说明
{{Fill in the Description}}

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

### <a name="parameters"></a>参数

#### <a name="-hostname"></a>-hostName
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Fill port Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>摘要
通过 AD 导入步骤创建跟踪文件

### <a name="syntax"></a>语法

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>说明
从给定 AD 水印检查点（分区 Cookie）跟踪 AAD Connect AD 导入运行的所有 ldap 查询。 在当前文件夹中创建跟踪文件“.\ADimportTrace_yyyyMMddHHmmss.log”。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
AD 连接器导出的 XML 文件

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
目标域控制器

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
林根 DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
要跟踪的 AD 对象类型 \>* = 所有对象类型

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

#### <a name="-adwatermark"></a>-ADwatermark
如果已以域管理员身份运行，则无需提供 AD 凭据。
手动输入水印，而不是使用 XML 文件，例如 $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>摘要
简短说明

### <a name="syntax"></a>语法

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>说明
长说明

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>示例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>参数

#### <a name="-context"></a>-Context
参数 1 帮助说明

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

#### <a name="-server"></a>-Server
参数 2 帮助说明

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
参数 2 帮助说明

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
参数 2 帮助说明

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。
有关详细信息，请参阅 about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216)。

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>摘要
使用新的 ConsistencyGuid (ImmutableId) 更新用户

### <a name="syntax"></a>语法

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>说明
使用取自 ConsistencyGuid 报告的新 ConsistencyGuid (ImmutableId) 值更新用户。此函数支持 WhatIf 开关。注意：必须导入制表符分隔的 ConsistencyGuid 报告

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>示例 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>参数

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
操作

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
日志文件的输出文件名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
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
