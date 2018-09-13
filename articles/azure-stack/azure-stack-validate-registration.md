---
title: 为 Azure Stack 验证 Azure 注册 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器来验证 Azure 注册。
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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 57869de8a99c65810da0c75f81c75d93eac88412
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998404"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册 
使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 验证 Azure 订阅是否已准备好与 Azure Stack 配合使用。 在开始 Azure Stack 部署之前，请验证注册。 就绪性检查器会验证下列项：
- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云服务提供商 (CSP) 或企业协议 (EA)。 
- 用来向 Azure 注册订阅的帐户可以登录到 Azure 并且是订阅所有者。 

有关 Azure Stack 注册的详细信息，请参阅[向 Azure 注册 Azure Stack](azure-stack-registration.md)。 

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具
下载 [PSGallery](https://aka.ms/AzsReadinessChecker) 上提供的 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 的最新版本。  

## <a name="prerequisites"></a>必备组件
必须满足以下先决条件。

**运行该工具的计算机：**
 - Windows 10 或 Windows Server 2016，具有 Internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmd，然后查看 *Major* 版本和 *Minor* 版本：  

    >`$PSVersionTable.PSVersion` 
 - 配置[适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 
 - 下载最新版[Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。  

**Azure Active Directory 环境：**
 - 标识将与 Azure Stack 配合使用的帐户的用户名和密码，该帐户必须是 Azure 订阅所有者。  
 - 标识将使用的 Azure 订阅的订阅 ID。 
 - 标识将使用的 AzureEnvironment：*AzureCloud*、*AzureGermanCloud* 或 *AzureChinaCloud*。

## <a name="validate-azure-registration"></a>验证 Azure 注册
1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker。
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. 从 PowerShell 提示符下，运行以下命令将 *$registrationCredential* 设置为身为订阅所有者的帐户。   将 *subscriptionowner@contoso.onmicrosoft.com* 替换为你的帐户和租户。 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. 从 PowerShell 提示符下，运行以下命令将 *$subscriptionID* 设置为要使用的 Azure 订阅。 将 *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* 替换为你自己的订阅 ID。  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. 从 PowerShell 提示符下，运行以下命令来启动对你的订阅的验证。 
   - 将 AzureEnvironment 的值指定为 *AzureCloud*、*AzureGermanCloud* 或 *AzureChinaCloud*。  
   - 提供 Azure Active Directory 管理员用户名和 Azure Active Directory 租户名称。 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. 运行该工具后，查看输出。 对于登录名和注册要求，确认状态都为“OK”。 成功的验证如下图所示：  
![运行验证](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>报表和日志文件
每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。 

这些文件可以帮助你在部署 Azure Stack 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。 

默认情况下，这两个文件都写入到 *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
 - 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。   
 - 可以在运行命令的末尾使用 **-CleanReport** 参数从 *AzsReadinessCheckerReport.json* 中清除  有关该工具的以前运行的信息。 有关详细信息，请参阅 [Azure Stack 验证报表](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败
如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 AzsReadinessChecker.log 中。

下面的示例针对常见的验证失败提供了指导。

### <a name="user-must-be-an-owner-of-the-subscription"></a>用户必须是订阅所有者   
![订阅所有者](./media/azure-stack-validate-registration/subscription-owner.png)
**原因** - 帐户不是 Azure 订阅的管理员。   

**解决方法** - 使用要根据 Azure Stack 部署中的资源使用量而被收费的 Azure 订阅的管理员帐户。


### <a name="expired-or-temporary-password"></a>过期的或临时密码 
![过期密码](./media/azure-stack-validate-registration/expired-password.png)
**原因** - 因为密码已过期或者是临时的，所以帐户无法登录。     

**解决方法** - 在 PowerShell 中，运行以下命令，然后根据提示来重置密码。 
  > `Login-AzureRMAccount` 

或者，以帐户身份登录到 https://portal.azure.com，将会强制用户更改密码。


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>不支持使用 Microsoft 帐户进行注册  
![不受支持的帐户](./media/azure-stack-validate-registration/unsupported-account.png)
**原因** - 指定了 Microsoft 帐户（例如 Outlook.com or Hotmail.com）。  这些帐户不受支持。

**解决方法** - 使用来自云服务提供商 (CSP) 或企业协议 (EA) 的帐户和订阅。 


### <a name="unknown-user-type"></a>未知用户类型  
![未知用户](./media/azure-stack-validate-registration/unknown-user.png)
**原因** - 帐户无法登录到指定的 Azure Active Directory 环境。 在本例中，将 *AzureChinaCloud* 指定为了 *AzureEnvironment*。  

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中，运行以下命令来验证帐户对特定的环境有效。     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>后续步骤
[验证 Azure 标识](azure-stack-validate-identity.md)
[查看就绪性报表](azure-stack-validation-report.md)
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)

