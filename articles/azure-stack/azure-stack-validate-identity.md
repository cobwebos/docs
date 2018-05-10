---
title: 验证 Azure 标识 Azure 堆栈 |Microsoft 文档
description: 使用 Azure 堆栈准备情况检查程序来验证 Azure 标识。
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
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-identity"></a>验证 Azure 标识 
使用 Azure 堆栈准备情况检查器工具 (AzsReadinessChecker) 来验证 Azure Active Directory (Azure AD) 是可以用于 Azure 堆栈。 在开始 Azure 堆栈部署之前，请验证您的 Azure 标识解决方案。  

准备情况检查程序会验证：
 - Azure Active Directory (Azure AD) 为 Azure 堆栈的标识提供程序。
 - 你打算使用的 Azure AD 帐户可以以 Azure Active Directory 的全局管理员身份登录。 

验证可确保你的环境准备好进行 Azure 堆栈，以在你的 Azure AD 中存储有关用户、 应用程序、 组和从 Azure 堆栈的服务主体的信息。

## <a name="get-the-readiness-checker-tool"></a>获取准备情况检查程序工具
从下载最新版本的 Azure 堆栈准备情况检查器工具 (AzsReadinessChecker) [PSGallery](https://aka.ms/AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件
以下先决条件必须到位。

**运行该工具的计算机：**
 - Windows 10 或 Windows Server 2016 中，在具有 internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell cmd，然后查看*主要*版本和*次要*版本：  

   > `$PSVersionTable.PSVersion`
 - 配置[PowerShell for Azure 堆栈](azure-stack-powershell-install.md)。 
 - 最新版本[Microsoft Azure 堆栈准备情况检查程序](https://aka.ms/AzsReadinessChecker)工具。

**Azure Active Directory 环境：**
 - 标识 Azure AD 帐户将用于 Azure 堆栈并确保其为 Azure Active Directory 全局管理员。
 - 标识你的 Azure AD 租户名称。 租户名称必须是*主*Azure Active Directory 域名。 例如， *contoso.onmicrosoft.com*。 
 - 确定你将使用 AzureEnvironement: *AzureCloud*， *AzureGermanCloud*，或*AzureChinaCloud*。

## <a name="validate-azure-identity"></a>验证 Azure 标识 
1. 满足的先决条件的计算机，打开的管理的 PowerShell 提示符，然后运行以下命令以安装 AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. 从 PowerShell 提示符下，运行以下命令以设置 *$serviceAdminCredential*作为服务管理员为你的 Azure AD 租户。  替换*serviceadmin@contoso.onmicrosoft.com*与你的帐户和租户。 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. 在 PowerShell 提示符下，运行以下命令以启动你的 Azure AD 的验证。 
   - 指定作为 AzureEnvironment 值*AzureCloud*， *AzureGermanCloud*，或*AzureChinaCloud*。  
   - 指定你 Azure Active Directory 租户的名称以替换*contoso.onmicrosoft.com*。 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. 运行该工具后，请查看输出。 确认状态是**确定**用于登录和安装要求。 成功验证显示如下图所示： 
 
![运行验证](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>报告和日志文件
每个时间验证运行时，它会记录到结果**AzsReadinessChecker.log**和**AzsReadinessCheckerReport.json**。 使用 PowerShell 中的验证结果显示这些文件的位置。

这些文件可以帮助你在部署 Azure 堆栈或调查验证问题之前共享验证状态。  这两个文件保留每个后续的验证检查的结果。 该报表提供您的标识配置的部署团队确认信息。 日志文件可帮助你调查验证问题的部署或支持团队。 

默认情况下，这两个文件写入到*C:\Users\<用户名 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
 - 使用 **-OutputPath** ***&lt;路径&gt;*** 运行的命令行，以指定不同的报表位置结尾的参数。   
 - 使用 **-CleanReport**参数运行的命令以清除信息从末尾*AzsReadinessCheckerReport.json*。  有关以前运行的工具。 

有关详细信息， [Azure 堆栈验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败
如果验证检查失败，在 PowerShell 窗口中显示有关失败的详细信息。 该工具还将信息记录到 AzsReadinessChecker.log。

下面的示例提供有关常见的验证失败的指导。

### <a name="expired-or-temporary-password"></a>过期的或临时密码 
 
![过期密码](./media/azure-stack-validate-identity/expired-password.png)
**原因**-帐户无法登录，因为密码已过期或是临时。     

**解析**-PowerShell 中运行以下命令，，然后按照提示操作以重置密码。  
> `Login-AzureRMAccount`

或者，登录到https://portal.azure.com如会强制更改密码的帐户和用户。
### <a name="unknown-user-type"></a>未知的用户类型 
 
![未知的用户](./media/azure-stack-validate-identity/unknown-user.png)
**原因**-帐户无法登录到指定的 Azure Active Directory (AADDirectoryTenantName)。 在此示例中， *AzureChinaCloud*指定为*AzureEnvironment*。

**解析**-确认该帐户是有效指定的 Azure 环境。 在 PowerShell 中，运行以下命令以验证该帐户是有效的特定的环境： Login-azurermaccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>不是管理员帐户。 
 
![不是管理员](./media/azure-stack-validate-identity/not-admin.png)

**可能的原因**-尽管可以成功地登录帐户，该帐户不是 Azure Active Directory (AADDirectoryTenantName) 的管理员。  

**解析**-登录到https://portal.azure.com作为帐户，请转到**Azure Active Directory** > **用户** > *选择用户*  > **目录角色**，并确保用户为**全局管理员**。  如果该帐户是用户，请转到**Azure Active Directory** > **自定义域**命名，并确认，您提供的名称为*AADDirectoryTenantName*是标记为此目录的主域名。  在此示例中，这是*contoso.onmicrosoft.com*。 

Azure 堆栈要求的域名的主域名。

## <a name="next-steps"></a>后续步骤
[验证 Azure 注册](azure-stack-validate-registration.md)  
[查看准备情况报表](azure-stack-validation-report.md)  
[常规 Azure 堆栈集成注意事项](azure-stack-datacenter-integration.md)  

