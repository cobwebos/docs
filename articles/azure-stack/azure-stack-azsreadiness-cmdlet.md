---
title: 开始 AzsReadinessChecker cmdlet 参考 |Microsoft 文档
description: Azure 堆栈准备情况检查程序模块的 PowerShell cmdlet 帮助。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937862"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>开始 AzsReadinessChecker cmdlet 参考

模块： Microsoft.AzureStack.ReadinessChecker

此模块只包含单个 cmdlet。  此 cmdlet 执行 Azure 堆栈的一个或多个预部署或预维护函数。

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





 ## <a name="description"></a>说明
**开始 AzsReadinessChecker** cmdlet 可验证证书、 Azure 帐户、 Azure 订阅和 Azure Active 目录。 运行验证之前部署 Azure 堆栈，或之前 Azure 堆栈维护机密旋转之类的操作。 该 cmdlet 还可用于基础结构证书并选择性地 PaaS 证书生成证书签名请求。  最后，该 cmdlet 可以重新打包修正打包的常见问题的 PFX 证书。

## <a name="examples"></a>示例
**示例： 生成证书签名请求**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

在此示例中，开始 AzsReadinessChecker 生成多个证书签名请求 (CSR) 的证书适用于使用的"东部"区域名称的 ADFS Azure 堆栈部署和"azurestack.contoso.com"的外部 FQDN

**示例： 验证证书**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

在此示例中，安全地 PFX 密码提示输入并开始 AzsReadinessChecker 检查有效有关 AAD 部署具有区域名称的"东部"和"azurestack.contoso.com"的外部 FQDN 的证书的相对文件夹"证书" 

**示例： 使用验证证书部署数据 （的部署和支持）**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
在此的部署和支持示例中，PFX 密码则会提示输入安全地并开始 AzsReadinessChecker 检查对标识、 区域和外部 FQDN 读取从部署有效的证书的相对文件夹"证书"生成部署数据的 JSON 文件。 

**示例： 验证 PaaS 证书**
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

在此示例中，哈希表构造使用路径和为每个 PaaS 证书的密码。 可以省略证书。 开始 AzsReadinessChecker 检查每个 PFX 路径是否存在以及验证它们东部使用区域和外部 FQDN azurestack.contoso.com。

**示例： PaaS 使用验证证书部署数据**
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

在此示例中，哈希表构造使用路径和为每个 PaaS 证书的密码。 可以省略证书。 开始 AzsReadinessChecker 检查每个 PFX 路径存在，并验证它们使用区域并且从生成部署的部署数据 JSON 文件中读取外部 FQDN。 

**示例： 验证 Azure 标识**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

在此示例中，服务管理员帐户凭据将提示你输入安全地并开始 AzsReadinessChecker 检查的 Azure 帐户和 Azure Active Directory 有效有关 AAD 部署与租户目录名称的"azurestack.contoso.com"


**示例： 验证部署数据 （部署支持） 与 Azure 标识**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

在此示例中，服务管理员帐户凭据安全地提示输入，然后开始 AzsReadinessChecker 检查的 Azure 帐户和 Azure Active Directory 有效 AAD 部署 AzureCloud 和 TenantName 读取从部署数据生成部署的 JSON 文件。


**示例： 验证 Azure 注册**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

在此示例中，订阅所有者凭据提示输入安全地和开始 AzsReadinessChecker 然后执行针对给定帐户的验证和订阅以确保它可以用于 Azure 堆栈注册。 


**示例： 验证部署数据 （部署团队） 向 Azure 注册**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，订阅所有者凭据提示输入安全地开始 AzsReadinessChecker 然后执行针对给定帐户的验证和订阅以确保它可用来进行 Azure 堆栈注册的其他详细信息从生成的部署的部署数据 JSON 文件中读取。

**示例： 导入/导出 PFX 包**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

在此示例中，PFX 密码则会提示输入安全。 将导入到本地计算机证书存储和重新使用同一密码导出和另存为 ssl_new.pfx ssl.pfx 文件。  此过程适用于使用证书验证标记，私钥没有的本地计算机属性集、 证书链已中断、 不相关的证书存在在 PFX 中或证书链，则以错误的顺序。


**示例： 查看验证报告 （部署支持）**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

此示例中，在部署或支持团队接收来自客户 (Contoso) 的准备情况报表，并使用开始 AzsReadinessChecker 查看 Contoso 执行的验证执行的状态。

**示例： 查看验证报告摘要证书验证仅 （的部署和支持）**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

此示例中，在部署或支持团队接收来自客户 Contoso 的准备情况报表，并使用开始 AzsReadinessChecker 查看汇总的 Contoso 执行证书验证执行的状态。



## <a name="required-parameters"></a>所需的参数
> -RegionName

指定 Azure 堆栈部署区域名称。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|接受管道输入：      |False         |
|接受通配符： |False         |

> -FQDN    

指定 Azure 堆栈部署的外部 FQDN，也别名为 ExternalFQDN 和 ExternalDomainName。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |ExternalFQDN ExternalDomainName |
|接受管道输入：      |False         |
|接受通配符： |False         |

 

> -IdentitySystem    

指定 Azure 堆栈部署标识系统的有效值，AAD 或 ADFS，对于 Azure Active Directory 和 Active Directory Federated Services 分别。
|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|有效值：               |AAD，ADFS  |
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

指定包含路径和 PaaS 证书将密码哈希表。
|  |  |
|----------------------------|---------|
|键入：                       |哈希表 |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -DeploymentDataJSONPath

指定 Azure 堆栈部署数据 JSON 配置文件。 为部署生成此文件。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -PfxPath

指定需要导入/导出例程，若要修复，此工具中的证书验证所述的问题证书的路径。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -ExportPFXPath  

指定从导入/导出例程生成的 PFX 文件的目标路径。  
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> 双使用者

证书请求生成指定的使用者的有序的字典。
|  |  |
|----------------------------|---------|
|键入：                       |OrderedDictionary   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RequestType

指定证书请求的 SAN 的类型。 有效值 MultipleCSR，SingleCSR。
- *MultipleCSR*生成多个证书请求，一个用于每个服务。
- *SingleCSR*生成的所有服务的一个证书请求。   

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |MultipleCSR'，'SingleCSR |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -OutputRequestPath

指定的目标路径对于证书请求文件目录必须已经存在。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AADServiceAdministrator

指定 Azure Active Directory 服务管理员用于 Azure 堆栈部署。
|  |  |
|----------------------------|---------|
|键入：                       |PSCredential   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AADDirectoryTenantName

指定要用于 Azure 堆栈部署的 Azure Active Directory 名称。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -AzureEnvironment

指定 Azure 服务包含帐户、 目录和订阅以用于 Azure 堆栈部署和注册的实例。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |AzureCloud'，'AzureChinaCloud，AzureGermanCloud |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RegistrationAccount

指定要用于 Azure 堆栈注册的注册帐户。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -RegistrationSubscriptionID

指定要用于 Azure 堆栈注册的注册订阅 ID。
|  |  |
|----------------------------|---------|
|键入：                       |Guid     |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

> -ReportPath

指定的准备情况报表路径，则默认为当前目录和默认报表名称。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |全部      |
|接受管道输入：      |False    |
|接受通配符： |False    |



## <a name="optional-parameters"></a>可选参数
> -CertificatePath     

指定在其下只有证书必需的证书文件夹存在的路径。

使用 Azure Active Directory 标识系统的 Azure 堆栈部署所需的文件夹是：

ACSBlob、 ACSQueue、 ACSTable，管理门户、 ARM 管理员 ARM 公共、 KeyVault，KeyVaultInternal，公共门户

对于 Azure 堆栈与 Active Directory 联合身份验证服务身份识别系统的部署被必需的文件夹：

ACSBlob、 ACSQueue、 ACSTable、 ADFS、 管理门户、 ARM 管理、 ARM 公共、 关系图、 KeyVault、 KeyVaultInternal、 公共门户


|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |。 \Certificates |
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

指定是否以仅显示报表摘要，省略详细信息。
|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |全部      |
|有效值：               |证书、 AzureRegistration、 AzureIdentity、 作业，'All' |
|接受管道输入：      |False    |
|接受通配符： |False    |


> -摘要 

指定是否以仅显示报表摘要，省略详细信息。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |


> -CleanReport  

删除以前的执行和验证历史记录，并验证写入新的报表。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |


> -OutputPath    

指定要保存准备情况 JSON 报表以及详细的日志文件的自定义路径。  如果路径不存在，则该工具将尝试创建目录。
|  |  |
|----------------------------|------------------|
|键入：                       |String            |
|职位:                   |名为             |
|默认值：              |$ENV: TEMP\AzsReadinessChecker  |
|接受管道输入：      |False             |
|接受通配符： |False             |


> -确认  

在运行 cmdlet 前进行确认的提示。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |


> -WhatIf  

显示在 cmdlet 运行时，会发生什么情况。 该 cmdlet 不会运行。
|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |wi                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

 
