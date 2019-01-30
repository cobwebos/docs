---
title: 为 Azure Stack 验证 Azure 标识 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器来验证 Azure 标识。
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
ms.openlocfilehash: 4fb636a91389309b44f2308efec1a6c257c41078
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242580"
---
# <a name="validate-azure-identity"></a>验证 Azure 标识 
使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 验证 Azure Active Directory (Azure AD) 是否已准备好与 Azure Stack 配合使用。 在开始 Azure Stack 部署之前，请验证 Azure 标识解决方案。  

就绪性检查器会验证下列项：
 - Azure Active Directory (Azure AD) 用作 Azure Stack 的标识提供程序。
 - 你打算使用的 Azure AD 帐户能够以 Azure Active Directory 的全局管理员身份登录。 

验证可以确保你的环境已针对 Azure Stack 做好了准备，可以在 Azure AD 中存储 Azure Stack 中的用户、应用程序、组和服务主体的相关信息。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具
请从 [PSGallery](https://aka.ms/AzsReadinessChecker) 下载 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 的最新版本。  

## <a name="prerequisites"></a>必备组件
必须满足以下先决条件。

**运行该工具的计算机：**
 - Windows 10 或 Windows Server 2016，具有 Internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmd，然后查看 *Major* 版本和 *Minor* 版本：  

   > `$PSVersionTable.PSVersion`
 - 配置[适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 
 - 最新版[Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

**Azure Active Directory 环境：**
 - 标识将用于 Azure Stack 的 Azure AD 帐户并确保它是 Azure Active Directory 全局管理员。
 - 标识 Azure AD 租户名称。 该租户名称必须是你的 Azure Active Directory 的“主”域名。 例如， *contoso.onmicrosoft.com*。 
 - 确定将使用的 AzureEnvironment。 支持的环境名称参数值为 AzureCloud、 AzureChinaCloud 或 AzureUSGovernment 具体取决于正在使用的 Azure 订阅。

## <a name="validate-azure-identity"></a>验证 Azure 标识 
1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker：  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. 从 PowerShell 提示符下，运行以下命令将 *$serviceAdminCredential* 设置为你的 Azure AD 租户的服务管理员。  将 *serviceadmin@contoso.onmicrosoft.com* 替换为你的帐户和租户。 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. 从 PowerShell 提示符下，运行以下命令来启动对 Azure AD 的验证。 
   - 为 AzureEnvironment 指定环境名称值。 支持的环境名称参数值为 AzureCloud、 AzureChinaCloud 或 AzureUSGovernment 具体取决于正在使用的 Azure 订阅。  
   - 指定 Azure Active Directory 租户名称替换*contoso.onmicrosoft.com*。 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. 运行该工具后，查看输出。 对于安装要求，确认状态为“正常”。 成功的验证如下图所示： 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```


## <a name="report-and-log-file"></a>报表和日志文件
每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

这些文件可以帮助你在部署 Azure Stack 之前共享验证状态，或者调查验证问题。  这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。 

默认情况下，这两个文件都写入到 *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
 - 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。   
 - 可以在运行命令的末尾使用 **-CleanReport** 参数从 *AzsReadinessCheckerReport.json* 中清除  有关该工具的以前运行的信息。 

有关详细信息，请参阅 [Azure Stack 验证报表](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败
如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 AzsReadinessChecker.log 中。

下面的示例针对常见的验证失败提供了指导。

### <a name="expired-or-temporary-password"></a>过期的或临时密码 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**原因** - 因为密码已过期或者是临时的，所以帐户无法登录。     

**解决方法** - 在 PowerShell 中，运行以下命令，然后根据提示来重置密码。  
> `Login-AzureRMAccount`

或者，以帐户身份登录到 https://portal.azure.com，将会强制用户更改密码。
### <a name="unknown-user-type"></a>未知用户类型 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**原因** - 帐户无法登录到指定的 Azure Active Directory (AADDirectoryTenantName)。 在本例中，将 *AzureChinaCloud* 指定为了 *AzureEnvironment*。

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中，运行以下命令来验证帐户对特定的环境有效： Login-azurermaccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>帐户不是管理员 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**原因** - 虽然帐户可以成功登录，但帐户不是 Azure Active Directory (AADDirectoryTenantName) 的管理员。  

**解决方法** - 以帐户身份登录到 https://portal.azure.com，转到“Azure Active Directory” > “用户” > “选择用户” > “目录角色”，然后确保该用户为**全局管理员**。  如果帐户是“用户”，请转到“Azure Active Directory” > “自定义域”名称，并确认你为“AADDirectoryTenantName”提供的名称已标记为此目录的主域名。  在此示例中，这是*contoso.onmicrosoft.com*。 

Azure Stack 要求域名是主域名。

## <a name="next-steps"></a>后续步骤
[验证 Azure 注册](azure-stack-validate-registration.md)  
[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  

