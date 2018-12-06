---
title: 为 Azure Stack 验证 Azure 注册 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器来验证 Azure 注册。
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
ms.reviewer: ''
ms.openlocfilehash: 9d84d8b40d0d3ebc58b86dbdc95ec737f13324af
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966396"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册
 
使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 验证 Azure 订阅是否已准备好与 Azure Stack 配合使用。 在开始 Azure Stack 部署之前，请验证注册。 就绪性检查器可验证：

- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云服务提供商 (CSP) 或企业协议 (EA)。 
- 用来向 Azure 注册订阅的帐户可以登录到 Azure 并且是订阅所有者。 

有关 Azure Stack 注册的详细信息，请参阅[向 Azure 注册 Azure Stack](azure-stack-registration.md)。 

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从下载 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 的最新版本[PowerShell 库](https://aka.ms/AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件

在位置必须满足以下先决条件：

**运行该工具的计算机：**
 - Windows 10 或 Windows Server 2016，具有 Internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell cmdlet，然后查看*主要*并*次要*版本：  

    ```powershell
    $PSVersionTable.PSVersion
    ``` 
 - 配置[适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 
 - 下载最新版[Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。  

**Azure Active Directory 环境：**
 - 标识将与 Azure Stack 配合使用的帐户的用户名和密码，该帐户必须是 Azure 订阅所有者。  
 - 标识将使用的 Azure 订阅的订阅 ID。 
 - 识别**AzureEnvironment**将使用。 支持的环境名称参数值为**AzureCloud**， **AzureChinaCloud**或**AzureUSGovernment**，具体取决于哪个 Azure 订阅使用。

## <a name="validate-azure-registration"></a>验证 Azure 注册

1. 在计算机上是否满足先决条件，打开管理 PowerShell 提示符并运行以下命令以安装**AzsReadinessChecker**。

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

2. 从 PowerShell 提示符处，运行以下命令，设置`$registrationCredential`是订阅所有者的帐户。 替换为`subscriptionowner@contoso.onmicrosoft.com`与你的帐户和租户： 
   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

3. 从 PowerShell 提示符处，运行以下命令，设置`$subscriptionID`将使用的 Azure 订阅。 替换为`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`你自己的订阅 id:
   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ``` 

4. 从 PowerShell 提示符处，运行以下命令以开始验证你的订阅： 
   - 将 AzureEnvironment 的值指定为 **AzureCloud**、**AzureGermanCloud** 或 **AzureChinaCloud**。  
   - 提供你的 Azure Active Directory 管理员和 Azure Active Directory 租户名称。 

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. 运行该工具后，查看输出。 对于登录名和注册要求，确认状态都为“OK”。 成功验证看起来类似于下面的示例：
  
   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。 

这些文件可以帮助你在部署 Azure Stack 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。 

默认情况下，这两个文件都写入到 *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*。  
 - 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。   
 - 可以在运行命令的末尾使用 **-CleanReport** 参数从 *AzsReadinessCheckerReport.json* 中清除  有关该工具的以前运行的信息。 有关详细信息，请参阅 [Azure Stack 验证报表](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败
如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还将信息记录到 AzsReadinessChecker.log 文件。

下面的示例提供常见的验证失败的指导：

### <a name="user-must-be-an-owner-of-the-subscription"></a>用户必须是订阅所有者   

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因**-的帐户不是 Azure 订阅的管理员。   

**解决方法** - 使用要根据 Azure Stack 部署中的资源使用量而被收费的 Azure 订阅的管理员帐户。

### <a name="expired-or-temporary-password"></a>过期的或临时密码
 
```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因**-因为密码已过期，或者是临时的帐户不能登录。     

**解决方法** - 在 PowerShell 中，运行以下命令，然后根据提示来重置密码。 

```powershell
Login-AzureRMAccount
``` 

或者，登录到 https://portal.azure.com因为将强制更改密码的帐户和用户。

### <a name="unknown-user-type"></a>未知用户类型  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因**-帐户无法登录到指定的 Azure Active Directory 环境。 在本例中，将 *AzureChinaCloud* 指定为了 *AzureEnvironment*。  

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中运行以下命令以验证该帐户对特定环境有效：
     
```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>后续步骤

- [验证 Azure 标识](azure-stack-validate-identity.md)
- [查看就绪性报表](azure-stack-validation-report.md)
- [有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)

