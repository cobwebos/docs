---
title: Azure AD Connect：ADConnectivityTools PowerShell 参考 | Microsoft Docs
description: 本文档提供 ADConnectivityTools.psm1 PowerShell 模块的参考信息。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.openlocfilehash: 7d4533f928470c0e468ea833d1ccfb6f7d78bb15
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498312"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect：ADConnectivityTools PowerShell 参考
以下文档提供了 Azure AD Connect 随附的 ADConnectivityTools.psm1 PowerShell 模块的参考信息。

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>摘要
检测本地 DNS 问题。

### <a name="syntax"></a>语法

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>说明
运行本地 DNS 连接测试。
若要配置 Active Directory 连接器，用户必须能够对尝试连接到林进行名称解析，同时，必须能够在与此林关联的域控制器中进行名称解析。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>示例 2
```
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
指定要测试的林的名称。

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

#### <a name="-dcs"></a>-DCs
指定要测试的 DC（域控制器）。

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
以 PSObject 的形式返回此诊断的结果。
使用此工具进行手动交互期间不需要指定。

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

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>摘要
确定指定的林是否存在。

### <a name="syntax"></a>语法

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>说明
在 DNS 服务器中查询与林关联的 IP 地址。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
指定要测试的林的名称。

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

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>摘要
验证 AD 林功能级别。

### <a name="syntax"></a>语法

#### <a name="samaccount"></a>SamAccount
```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN
```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>说明
验证 AD 林功能级别是等于还是超过给定的 MinAdForestVersion (WindowsServer2003)。
可以请求帐户（域\用户名）和密码。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>示例 2
```
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>示例 3
```
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
目标林。
默认值是当前登录用户的林。

```yaml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN
目标 ForestFQDN 对象。

```yaml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
该函数将使用当前已登录计算机的用户的凭据，而不是从用户请求自定义凭据。

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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>摘要
检测本地网络连接问题。

### <a name="syntax"></a>语法

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>说明
运行本地网络连接测试。

对于本地网络测试，AAD Connect 必须能够与端口 53 (DNS)、88 (Kerberos) 和 389 (LDAP) 上的命名域控制器通信。大多数组织在其 DC 上运行 DNS，这正是目前集成了此测试的原因。
如果指定了另一台 DNS 服务器，则应跳过端口 53。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>示例 2
```
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-dcs"></a>-DCs
指定要测试的 DC（域控制器）。

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort
如果用户未使用 AD 站点/登录 DC 提供的 DNS 服务，则他（她）可能需要跳过端口 53 的检查。 用户仍必须能够解析 _ldap._tcp.\<forestfqdn\>，才能让 Active Directory 连接器配置成功。

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
以 PSObject 的形式返回此诊断的结果。
使用此工具进行手动交互期间不需要指定。

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

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>摘要
确定是否可访问指定的林及其关联的域控制器。

### <a name="syntax"></a>语法

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>说明
运行“ping”测试（某台计算机是否可以通过网络和/或 Internet 访问目标计算机）

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>示例 2
```
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
指定要测试的林的名称。

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

#### <a name="-dcs"></a>-DCs
指定要测试的 DC（域控制器）。

```yaml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>摘要
验证是否可访问所获取的林 FQDN 中的域

### <a name="syntax"></a>语法

#### <a name="samaccount"></a>SamAccount
```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN
```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>说明
通过尝试检索 DomainGuid 和 DomainDN，验证是否可访问所获取的林 FQDN 中的所有域。
可以请求帐户（域\用户名）和密码。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>示例 2
```
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>示例 3
```
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
目标林。

```yaml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN
目标 ForestFQDN 对象。

```yaml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
该函数将使用当前已登录计算机的用户的凭据，而不是从用户请求自定义凭据。

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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>摘要
验证用户是否有企业管理员凭据。

### <a name="syntax"></a>语法

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>说明
通过搜索验证提供的用户是否有企业管理员凭据。
可以请求帐户（域\用户名）和密码。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>示例 2
```
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
该函数将使用当前已登录计算机的用户的凭据，而不是从用户请求自定义凭据。

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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>摘要
从帐户和密码组合中检索 DomainFQDN。

### <a name="syntax"></a>语法

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>说明
尝试从提供的凭据中获取 domainFQDN 对象。
如果 domainFQDN 有效，则会根据用户的选择返回 DomainFQDNName 或 RootDomainName。
可以请求帐户（域\用户名）和密码。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>示例 2
```
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>参数

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType
要检索的所需数据类型。
目前限制为“DomainFQDNName”或“RootDomainName”。

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

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
该函数将使用当前已登录计算机的用户的凭据，而不是从用户请求自定义凭据。

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

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError
Start-NetworkConnectivityDiagnosisTools 函数使用的辅助参数

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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>摘要
从帐户和密码组合中检索 ForestFQDN。

### <a name="syntax"></a>语法

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>说明
尝试从提供的凭据中获取 ForestFQDN。
可以请求帐户（域\用户名）和密码。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>示例 2
```
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
目标林。默认值是当前登录用户的域。

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

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials
该函数将使用当前已登录计算机的用户的凭据，而不是从用户请求自定义凭据。

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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>摘要
主函数。

### <a name="syntax"></a>语法

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>说明
运行用于验证 AD 凭据是否有效的所有可用机制。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
目标林。

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

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount
对于自定义安装：如果用户在 AADConnect 向导的“AD 林帐户”窗口中选择了“创建新的 AD 帐户”，则该标志的值为 $True。
如果用户选择了“使用现有 AD 帐户”，则值为 $False。
对于快速安装：此变量的值必须为 $True。

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
请求用户的凭据时在“用户名”字段中预填充的参数。

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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>摘要
用于网络连接测试的主函数。

### <a name="syntax"></a>语法

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>说明
运行本地网络连接测试。

### <a name="examples"></a>示例

#### <a name="example-1"></a>示例 1
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>示例 2
```
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>参数

#### <a name="-forest"></a>-Forest
指定要测试的林名称。

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

#### <a name="-credentials"></a>-Credentials
运行测试的用户的用户名和密码。
该用户的权限级别必须与运行 Azure AD Connect 向导所需的权限级别相同。

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

#### <a name="-logfilelocation"></a>-LogFileLocation
指定包含此函数的输出的日志文件的位置。

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

#### <a name="-dcs"></a>-DCs
指定要测试的 DC（域控制器）。

```yaml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage
一个标志，用于显示有关此函数的用途的消息。

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject
以 PSObject 的形式返回此诊断的结果。
使用此工具进行手动交互期间不需要指定。

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

#### <a name="-validcredentials"></a>-ValidCredentials
指示用户键入的凭据是否有效。
使用此工具进行手动交互期间不需要指定。

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
