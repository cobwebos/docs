---
title: "注册 Azure 堆栈 |Microsoft 文档"
description: "向你的 Azure 订阅中注册 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2018
ms.author: jeffgilb
ms.openlocfilehash: b58b3fc538d2237c12a860d268d550c4223155ba
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>注册你的 Azure 订阅的 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以注册[Azure 堆栈](azure-stack-poc.md)与 Azure 下载从 Azure 应用商店项以及设置回向 Microsoft 报告的商业数据。

> [!NOTE]
>建议注册，因为它使你可以测试重要 Azure 堆栈功能，例如 marketplace 联合和使用情况报告。 注册 Azure 堆栈后，使用情况报告给 Azure 商务。 你可以在用于注册的订阅下看到它。 Azure 堆栈开发工具包用户不收取任何使用这些报告。


## <a name="get-azure-subscription"></a>获取 Azure 订阅

在之前向 Azure 注册 Azure 堆栈，您必须：

- Azure 订阅的订阅 ID。 若要获取的 ID，登录到 Azure，单击**更多的服务** > **订阅**，单击你想要使用，的订阅并在列表视图**Essentials**可以找到**订阅 ID**。 当前不支持中国、 德国和我们政府云订阅。
- 用户名和密码是订阅的所有者的帐户 （MSA/2FA 帐户也支持）。
- *不需要从 Azure 堆栈 1712年更新版本 (180106.1) 开始：*的 Azure 订阅的 Azure Active Directory。 通过将鼠标悬停在 Azure 门户右上角用户标识，可以在 Azure 中找到此目录。

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/en-us/free/?b=17.06)。 注册 Azure 堆栈会产生任何费用在你的 Azure 订阅。

## <a name="register-azure-stack-with-azure"></a>向 Azure 注册 Azure 堆栈  
> [!NOTE]
> 必须具有访问特权终结点的计算机从运行所有这些步骤。 对于 Azure 堆栈开发工具包，它将是主计算机。 如果你使用的集成的系统，请联系 Azure 堆栈运算符。
>

1. 打开 PowerShell 控制台以管理员身份和[安装适用于 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。  

2. 添加用于注册 Azure 堆栈的 Azure 帐户。 若要添加的帐户，运行`Add-AzureRmAccount`EnvironmentName 参数设置为 cmdlet **AzureCloud**。 系统将提示你输入你的 Azure 帐户凭据，并且你可能必须为使用双因素身份验证，具体情况视你的帐户的配置。

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. 如果你有多个订阅，运行以下命令以选择你想要使用：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令以在你的 Azure 订阅中注册 Azure 堆栈资源提供程序：

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 删除任何现有版本对应于注册的 PowerShell 模块和[从 GitHub 下载的最新版本](azure-stack-powershell-download.md)。  

6. 从"AzureStack 工具主机"目录创建与前面步骤中，导航到"注册"文件夹，然后导入".\RegisterWithAzure.psm1"模块：  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. 在相同的 PowerShell 会话中，运行以下脚本： 提示输入凭据时，指定`azurestack\cloudadmin`作为用户和密码是什么你使用相同的本地管理员在部署过程。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | 参数 | 说明 |  
   |--------|-------------|
   | CloudAdminCredential | 经常使用的云域凭据[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。 用户名采用以下格式 **\<Azure 堆栈域\>\cloudadmin**。 对于开发工具包，用户名将设置为**azurestack\cloudadmin**。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。|  
   | PrivilegedEndpoint | 预配置远程 PowerShell 控制台，它提供功能，如日志收集和其他文章部署任务。 开发工具包"AzS ERCS01"虚拟机上托管特权终结点。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。 若要了解详细信息，请参见[使用特权终结点](azure-stack-privileged-endpoint.md)文章。|  
   | BillingModel | 使用你的订阅的计费模型。 允许的值为此参数是-**容量**， **PayAsYouUse**，和**开发**。 为开发工具包，此值设置为**开发**。 如果你使用的集成的系统，请联系你的 Azure 堆栈运算符来获取此值。 |

8. 完成脚本后，你将看到一条消息"正在激活 Azure 堆栈 （此步骤可能需要 10 分钟的时间才能完成）。"




## <a name="verify-the-registration"></a>验证注册  

1. 登录到管理员门户 (https://adminportal.local.azurestack.external)。

2. 单击**更多的服务** > **应用商店管理** > **添加从 Azure**。

3. 如果你看到的项 （如 WordPress) 的 Azure 上提供的列表，已成功激活。

> [!NOTE]
> 完成注册后，将不再显示未注册的活动警告。


## <a name="modify-the-registration"></a>修改的注册

### <a name="change-the-subscription-you-use"></a>更改你使用的订阅
如果你想要更改你使用的订阅，首先必须运行删除 AzsRegistration，将确保你已登录到正确的 Azure PowerShell 上下文中，并集 AzsRegistration 然后进行任何更改的参数。

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>更改计费模型或联合功能
如果你想要更改计费模型或你安装的联合功能，你可以调用要设置新值的注册函数。 不需要先删除当前的注册。  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>断开连接的注册
*此部分中的信息适用 Azure 堆栈 1712年更新版本 (180106.1) 开头，并且不支持与早期版本。*

如果你要在连接断开的环境中注册 Azure 堆栈，你需要从 Azure 堆栈环境中获取了注册令牌，然后在可以连接到 Azure 进行注册的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure 堆栈环境中获取了注册令牌
  1. 若要获取注册令牌，请运行以下命令：  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 注册令牌保存在为指定的文件*$env:SystemDrive\RegistrationToken.txt*。

  2. 保存在 Azure 上使用此注册令牌机的连接。


### <a name="connect-to-azure-and-register"></a>连接到 Azure 以及注册
可以连接到 Azure 的计算机上运行此过程的步骤。

  1. [从 GitHub 下载到注册的相对应的最新 PowerShell 模块](azure-stack-powershell-download.md)。  

  2. 从"AzureStack 工具主机"目录创建与前面步骤中，导航到"注册"文件夹，然后导入".\RegisterWithAzure.psm1"模块：  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. 复制[RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959)到文件夹，如*C:\Temp*。

  4. 以管理员身份启动 PowerShell ISE，然后导入 RegisterWithAzure 模块。  

  5. 确保你登录到你想要用于注册你的 Azure 堆栈环境的正确的 Azure PowerShell 上下文：  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. 指定要向 Azure 注册的注册令牌：

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    （可选） 你可以使用 Get-content cmdlet 点到包含您的注册令牌的文件：

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> 保存注册资源名称或以供将来参考的注册令牌。

### <a name="remove-a-registered-resource"></a>删除已注册的资源
如果你想要删除注册资源，然后必须使用注销 AzsEnvironment 并传递注册资源名称或用于注册 AzsEnvironment 注册令牌中。
- **注册资源名称：**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **注册令牌：**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>后续步骤
[连接到 Azure Stack](azure-stack-connect-azure-stack.md)
