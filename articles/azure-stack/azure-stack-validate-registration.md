---
title: 验证 Azure 注册的 Azure 堆栈 |Microsoft 文档
description: 使用 Azure 堆栈准备情况检查程序来验证 Azure 注册。
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937836"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册 
使用 Azure 堆栈准备情况检查器工具 (AzsReadinessChecker) 来验证你的 Azure 订阅已准备好使用与 Azure 堆栈。 在开始 Azure 堆栈部署之前，请验证注册。 准备情况检查程序会验证：
- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云服务提供程序 (CSP) 或企业协议 (EA)。 
- 用于向 Azure 注册你的订阅的帐户可以登录到 Azure，并为某一订阅所有者。 

有关 Azure 堆栈注册的详细信息，请参阅[与 Azure 注册 Azure 堆栈](azure-stack-registration.md)。 

## <a name="get-the-readiness-checker-tool"></a>获取准备情况检查程序工具
下载 Azure 堆栈准备情况检查器工具 (AzsReadinessChecker) 的最新版本位于[PSGallery](https://aka.ms/AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件
以下先决条件必须到位。

**运行该工具的计算机：**
 - Windows 10 或 Windows Server 2016 中，在具有 internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell cmd，然后查看*主要*版本和*次要*版本：  

    >`$PSVersionTable.PSVersion` 
 - 配置[PowerShell for Azure 堆栈](azure-stack-powershell-install.md)。 
 - 下载最新版本的[Microsoft Azure 堆栈准备情况检查程序](https://aka.ms/AzsReadinessChecker)工具。  

**Azure Active Directory 环境：**
 - 标识的用户名和密码是你将使用与 Azure 堆栈的 Azure 订阅的所有者的帐户。  
 - 确定你将使用的 Azure 订阅的订阅 ID。 
 - 确定你将使用 AzureEnvironment: *AzureCloud*， *AzureGermanCloud*，或*AzureChinaCloud*。

## <a name="validate-azure-registration"></a>验证 Azure 注册
1. 满足的先决条件的计算机，打开的管理的 PowerShell 提示符，然后运行以下命令以安装 AzsReadinessChecker。
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. 从 PowerShell 提示符下，运行以下命令以设置 *$registrationCredential*是订阅所有者的帐户。   替换*subscriptionowner@contoso.onmicrosoft.com*与你的帐户和租户。 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. 从 PowerShell 提示符下，运行以下命令以设置 *$subscriptionID*将使用的 Azure 订阅中。 替换*xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx*替换为订阅 id。  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. 从 PowerShell 提示符下，运行以下命令以启动验证你的订阅 
   - 指定作为 AzureEnvironment 值*AzureCloud*， *AzureGermanCloud*，或*AzureChinaCloud*。  
   - 提供你的 Azure Active Directory 管理员和你的 Azure Active Directory 租户名称。 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. 运行该工具后，请查看输出。 确认状态用于登录和注册要求是确定。 成功验证显示如下图所示：  
![运行验证](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>报告和日志文件
每个时间验证运行时，它会记录到结果**AzsReadinessChecker.log**和**AzsReadinessCheckerReport.json**。 使用 PowerShell 中的验证结果显示这些文件的位置。 

这些文件可以帮助你在部署 Azure 堆栈或调查验证问题之前共享验证状态。 这两个文件保留每个后续的验证检查的结果。 该报表提供您的标识配置的部署团队确认信息。 日志文件可帮助你调查验证问题的部署或支持团队。 

默认情况下，这两个文件写入到*C:\Users\<用户名 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
 - 使用 **-OutputPath** ***&lt;路径&gt;*** 运行的命令行，以指定不同的报表位置结尾的参数。   
 - 使用 **-CleanReport**参数运行的命令以清除信息从末尾*AzsReadinessCheckerReport.json*。  有关以前运行的工具。 有关详细信息， [Azure 堆栈验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败
如果验证检查失败，在 PowerShell 窗口中显示有关失败的详细信息。 该工具还将信息记录到 AzsReadinessChecker.log。

下面的示例提供有关常见的验证失败的指导。

### <a name="user-must-be-an-owner-of-the-subscription"></a>用户必须是订阅的所有者   
![订阅所有者](./media/azure-stack-validate-registration/subscription-owner.png)
**原因**-该帐户不是 Azure 订阅的管理员。   

**解析**的使用是的使用情况从 Azure 堆栈部署将继续计费的 Azure 订阅的管理员的帐户。


### <a name="expired-or-temporary-password"></a>过期的或临时密码 
![过期密码](./media/azure-stack-validate-registration/expired-password.png)
**原因**-帐户无法登录，因为密码已过期或是临时。     

**解析**-PowerShell 中运行并按照提示重置的密码。 
  > `Login-AzureRMAccount` 

或者，登录到https://portal.azure.com如会强制更改密码的帐户和用户。


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>注册不支持 Microsoft 帐户  
![不受支持的帐户](./media/azure-stack-validate-registration/unsupported-account.png)
**原因**-指定 Microsoft 帐户 （例如 Outlook.com 或 Hotmail.com）。  不支持这些帐户。

**解析**-使用的帐户和订阅的云服务提供程序 (CSP) 或企业协议 (EA)。 


### <a name="unknown-user-type"></a>未知的用户类型  
![未知用户](./media/azure-stack-validate-registration/unknown-user.png)
**原因**-帐户无法登录到指定的 Azure Active Directory 环境。 在此示例中， *AzureChinaCloud*指定为*AzureEnvironment*。  

**解析**-确认该帐户是有效指定的 Azure 环境。 在 PowerShell 中，运行以下命令以验证该帐户是有效的特定的环境。     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>后续步骤
[验证 Azure 标识](azure-stack-validate-identity.md)
[查看准备情况报表](azure-stack-validation-report.md)
[常规 Azure 堆栈集成注意事项](azure-stack-datacenter-integration.md)

