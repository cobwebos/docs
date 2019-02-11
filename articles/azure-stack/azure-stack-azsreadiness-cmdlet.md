---
title: Start-AzsReadinessChecker cmdlet 参考 | Microsoft Docs
description: Azure Stack 就绪性检查器模块的 PowerShell cmdlet 帮助。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 861784a2d22d15253c61884bfab62667c0560bcd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250633"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker cmdlet 参考

模块：Microsoft.AzureStack.ReadinessChecker

此模块只包含一个 cmdlet。  此 cmdlet 针对 Azure Stack 执行一个或多个部署前或服务前函数。

## <a name="syntax"></a>语法

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>描述

**Start-AzsReadinessChecker** cmdlet 验证证书、Azure 帐户、Azure 订阅和 Azure Active Directory。 在部署 Azure Stack 之前或在运行 Azure Stack 服务操作（例如机密轮换）之前运行验证。 该 cmdlet 还可用于生成基础结构证书的证书签名请求，以及 PaaS 证书（可选）。  最后，该 cmdlet 可以重新打包 PFX 证书，以解决常见的打包问题。

## <a name="examples"></a>示例

### <a name="example-generate-certificate-signing-request"></a>示例：生成证书签名请求

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

在此示例中，Start-AzsReadinessChecker 针对区域名称为“east”、外部 FQDN 为“azurestack.contoso.com”的 ADFS Azure Stack 部署所适用的证书生成多个证书签名请求 (CSR)

### <a name="example-validate-certificates"></a>示例：验证证书

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

在此示例中，系统以安全方式提示输入 PFX 密码，Start-AzsReadinessChecker 在相对文件夹“Certificates”中检查区域名称为“east”、外部 FQDN 为“azurestack.contoso.com”的 AAD 部署是否有适用的有效证书

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>示例：使用部署数据验证证书（部署和支持）

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

在此部署和支持示例中，系统以安全方式提示输入 PFX 密码，Start-AzsReadinessChecker 在相对文件夹“Certificates”中检查标识、区域和外部 FQDN 都是从针对部署生成的部署数据 JSON 文件中读取的部署是否有适用的有效证书。 

### <a name="example-validate-paas-certificates"></a>示例：验证 PaaS 证书

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

此示例使用每个 PaaS 证书的路径和密码构造了哈希表。 可以省略证书。 Start-AzsReadinessChecker 检查每个 PFX 路径是否存在，并使用区域“east”和外部 FQDN“azurestack.contoso.com”验证这些路径。

### <a name="example-validate-paas-certificates-with-deployment-data"></a>示例：使用部署数据验证 PaaS 证书

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

此示例使用每个 PaaS 证书的路径和密码构造了哈希表。 可以省略证书。 Start-AzsReadinessChecker 检查每个 PFX 路径是否存在，并使用从针对部署生成的部署数据 JSON 文件中读取的区域和外部 FQDN 验证这些路径。 

### <a name="example-validate-azure-identity"></a>示例：验证 Azure 标识

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

在此示例中，系统以安全方式提示输入服务管理员帐户凭据，Start-AzsReadinessChecker 检查 Azure 帐户和 Azure Active Directory 是否适用于租户目录名称为“azurestack.contoso.com”的 AAD 部署

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>示例：使用部署数据验证 Azure 标识（部署支持）

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

在此示例中，服务管理员帐户凭据会提示输入安全地和 Start-azsreadinesschecker 检查 Azure 帐户和 Azure Active Directory 有效的 AAD 部署 AzureCloud 和 TenantName 读取从部署数据为部署生成的 JSON 文件。

### <a name="example-validate-azure-registration"></a>示例：验证 Azure 注册

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

在此示例中，系统以安全方式提示输入订阅所有者凭据，然后，Start-AzsReadinessChecker 对给定的帐户和订阅执行验证，以确保它们可用于 Azure Stack 注册。 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>示例：使用部署数据验证 Azure 注册（部署团队）

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，系统以安全方式提示输入订阅所有者凭据，然后，Start-AzsReadinessChecker 对给定的帐户和订阅执行验证，以确保它们可用于 Azure Stack 注册（该注册的其他详细信息从针对部署生成的部署数据 JSON 文件中读取）。

### <a name="example-importexport-pfx-package"></a>示例：导入/导出 PFX 包

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

在此示例中，系统以安全方式提示输入 PFX 密码。 ssl.pfx 文件 将导入到本地计算机证书存储，然后以相同的密码重新导出，并保存为 ssl_new.pfx。  此过程适用于证书验证以标志指出私钥中未设置本地计算机属性、证书链中断、PFX 中存在不相关的证书，或证书链顺序错误等情况。

### <a name="example-view-validation-report-deployment-support"></a>示例：查看验证报告（部署支持）

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

在此示例中，部署或支持团队从客户 (Contoso) 接收就绪性报告，并使用 Start-AzsReadinessChecker 查看 Contoso 的验证执行状态。

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>示例：仅查看证书验证的验证报告摘要（部署和支持）

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

在此示例中，部署或支持团队从客户 (Contoso) 接收就绪性报告，并使用 Start-AzsReadinessChecker 查看 Contoso 的证书验证执行的摘要状态。

## <a name="required-parameters"></a>必需的参数

> -RegionName

指定 Azure Stack 部署的区域名称。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|接受管道输入：      |False         |
|接受通配符： |False         |

> -FQDN

指定 Azure Stack 部署的外部 FQDN，也可以使用别名 ExternalFQDN 和 ExternalDomainName。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |ExternalFQDN、ExternalDomainName |
|接受管道输入：      |False         |
|接受通配符： |False         |

> -IdentitySystem

指定 Azure Stack 部署的标识系统有效值：AAD 或 ADFS（分别表示 Azure Active Directory 和 Active Directory 联合身份验证服务）。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|有效值：               |'AAD'、'ADFS'  |
|接受管道输入：      |False         |
|接受通配符： |False         |

> -PfxPassword

指定与 PFX 证书文件关联的密码。
|  |  |
|----------------------------|---------|
|键入：                       |SecureString |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -PaaSCertificates

指定包含 PaaS 证书路径和密码的哈希表。
|  |  |
|----------------------------|---------|
|键入：                       |Hashtable |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -DeploymentDataJSONPath

指定 Azure Stack 部署数据 JSON 配置文件。 此文件是针对部署生成的。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -PfxPath

指定需要通过导入/导出例程修复的有问题的证书（此工具中的证书验证会指出该问题）的路径。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -ExportPFXPath  

指定导入/导出例程生成的 PFX 文件的目标路径。  
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -Subject

指定用于生成证书请求的主题的排序字典。
|  |  |
|----------------------------|---------|
|键入：                       |OrderedDictionary   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RequestType

指定证书请求的 SAN 类型。 有效值为 MultipleCSR、SingleCSR。

- *MultipleCSR* 生成多个证书请求，针对每个服务各生成一个。
- *SingleCSR* 为所有服务生成一个证书请求。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |'MultipleCSR'、'SingleCSR' |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -OutputRequestPath

指定证书请求文件的目标路径，目录必须已存在。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AADServiceAdministrator

指定用于 Azure Stack 部署的 Azure Active Directory 服务管理员。
|  |  |
|----------------------------|---------|
|键入：                       |PSCredential   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AADDirectoryTenantName

指定用于 Azure Stack 部署的 Azure Active Directory 名称。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AzureEnvironment

指定用于 Azure Stack 部署和注册的帐户、目录和订阅所在的 Azure 服务实例。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |AzureCloud，AzureChinaCloud，AzureUSGovernment |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RegistrationAccount

指定用于 Azure Stack 注册的注册帐户。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RegistrationSubscriptionID

指定用于 Azure Stack 注册的注册订阅 ID。
|  |  |
|----------------------------|---------|
|键入：                       |Guid     |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -ReportPath

指定就绪性报告的路径，默认值为当前目录和默认报告名称。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |All      |
|接受管道输入：      |False    |
|接受通配符： |False    |

## <a name="optional-parameters"></a>可选参数

> -CertificatePath

指定只包含证书所需证书文件夹的路径。

包含 Azure Active Directory 标识系统的 Azure Stack 部署的所需文件夹为：

ACSBlob、ACSQueue、ACSTable、Admin Portal、ARM Admin、ARM Public、KeyVault、KeyVaultInternal、Public Portal

包含 Active Directory 联合身份验证服务标识系统的 Azure Stack 部署的所需文件夹为：

ACSBlob、ACSQueue、ACSTable、ADFS、Admin Portal、ARM Admin、ARM Public、Graph、KeyVault、KeyVaultInternal、Public Portal

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |.\Certificates |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -IncludePaaS  

指定是否应将 PaaS 服务/主机名添加到证书请求。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

> -ReportSections

指定是否仅显示报告摘要，并省略详细信息。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |All      |
|有效值：               |'Certificate'、'AzureRegistration'、'AzureIdentity'、'Jobs'、'All' |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -Summary

指定是否仅显示报告摘要，并省略详细信息。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

> -CleanReport

删除以往的执行和验证历史记录，并将验证结果写入新报告。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

> -OutputPath

指定用于保存就绪性 JSON 报告和详细日志文件的自定义路径。  如果该路径不存在，工具会尝试创建目录。

|  |  |
|----------------------------|------------------|
|键入：                       |String            |
|职位:                   |名为             |
|默认值：              |$ENV:TEMP\AzsReadinessChecker  |
|接受管道输入：      |False             |
|接受通配符： |False             |

> -Confirm

提示你在运行 cmdlet 之前进行确认。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

> -WhatIf

显示在此 cmdlet 运行的情况下将会发生什么。 此 cmdlet 未运行。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |wi                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |
