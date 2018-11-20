---
title: 将 ASDK 注册到 Azure | Microsoft Docs
description: 介绍如何将 Azure Stack 注册到 Azure，以实现市场联合并报告使用情况。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e86ff4ebf91d0c0b691caf429d9489bf769f16af
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975186"
---
# <a name="azure-stack-registration"></a>Azure Stack 注册
可将 Azure Stack 开发工具包 (ASDK) 安装注册到 Azure，以便从 Azure 下载市场项，并设置向 Microsoft 报告商务数据的功能。 需要注册才能支持完整的 Azure Stack 功能，包括市场联合。 之所以建议注册，是因为这样可以测试重要的 Azure Stack 功能，例如市场联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户无需付费，不管他们报告的用量是多少。

如果未注册 ASDK，则可能会看到“需要激活”警告警报，建议注册 Azure Stack 开发工具包。 此行为是预期的行为。

## <a name="prerequisites"></a>必备组件
在遵照这些说明将 ASDK 注册到 Azure 之前，请确保已安装 Azure Stack PowerShell，并已下载[部署后配置](asdk-post-deploy.md)一文中所述的 Azure Stack 工具。

此外，在用于向 Azure 注册 ASDK 的计算机上，PowerShell 语言模式必须设置为 **FullLanguageMode**。 若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell 命令：

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguageMode**。 如果返回了其他任何语言模式，则需要在另一台计算机上运行注册，或者将语言模式设置为 **FullLanguageMode**，然后才能继续。

## <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure
遵循以下步骤将 ASDK 注册到 Azure。

> [!NOTE]
> 所有这些步骤必须在可以访问特权终结点的计算机上运行。 对于 ASDK 来说，该计算机是开发工具包主机。

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure。 需要同时登录到 Azure 订阅和本地 ASDK 安装。 如果还没有 Azure 订阅，你可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。<br><br>如果使用同一 Azure 订阅 ID 在 Azure Stack 的多个实例上运行注册脚本，请在运行 **Set-AzsRegistration** cmdlet 时为注册设置唯一的名称。 **RegistrationName** 参数的默认值为 **AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用同一名称，该脚本会失败。

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. 该脚本完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

    ![环境现已注册](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>在断开连接的环境中注册
如果 （未连接到 internet)，要在连接断开的环境中注册 Azure Stack，则需要从 Azure Stack 环境获取注册令牌，然后在可以连接到 Azure 以注册并创建激活的计算机上使用该令牌ASDK 环境的资源。
 
 > [!IMPORTANT]
 > 在之前使用这些说明来注册 Azure Stack，请确保已安装了适用于 Azure Stack 的 PowerShell 并下载 Azure Stack 工具，如中所述[部署后配置](asdk-post-deploy.md)ASDK 主机上的文章计算机和用于连接到 Azure 并注册的 internet 访问的计算机。

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌
在 ASDK 主机计算机上以管理员身份启动 PowerShell 并导航到**注册**中的文件夹**AzureStack 工具主**时下载 Azure Stack 工具创建的目录。 使用以下 PowerShell 命令来导入**RegisterWithAzure.psm1**模块，然后使用**Get AzsRegistrationToken** cmdlet 来获取注册令牌：  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```
默认情况下，注册令牌保存在为指定的文件 *$FilePathForRegistrationToken*参数。 可以自行更改文件路径或文件名。

将使用此注册令牌保存在连接 internet 的计算机上。 可以从 $FilePathForRegistrationToken 复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册
在连接 internet 的计算机，以管理员身份启动 PowerShell 并导航到**注册**中的文件夹**AzureStack 工具主**创建时下载 Azure 目录堆栈的工具。 使用以下 PowerShell 命令来导入**RegisterWithAzure.psm1**模块，然后使用**Register-azsenvironment** cmdlet 来注册到 Azure，从而注册令牌只需创建和唯一注册名称：  

  ```PowerShell  
  $registrationToken = "<your registration token>"
  $RegistrationName = "<unique registration name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

或者，可以使用**Get-content** cmdlet 以指向文件，其中包含您的注册令牌：

  ```PowerShell  
  $registrationToken = Get-Content -Path '<path>\<registration token file>'
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

保存注册令牌并注册资源名称以供将来参考。

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

仍在使用连接到 internet 的计算机，从注册到 Azure 时创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell 命令，使用上一步中注册到 Azure 时提供相同的唯一注册名称值：  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

激活密钥保存在为 *$KeyOutputFilePath* 指定的文件中。 可以自行更改文件路径或文件名。

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

与文件返回到 Azure Stack 环境或从创建的激活密钥中的文本**Get AzsActivationKey**。 运行以下 PowerShell 命令，在使用该激活密钥的 Azure Stack 中创建激活资源：   

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

或者，可以使用**Get-content** cmdlet 以指向文件，其中包含您的注册令牌：

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = Get-Content -Path '<path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

## <a name="verify-the-registration-was-successful"></a>验证注册是否成功
遵循以下步骤来验证 ASDK 是否已成功注册到 Azure。

1. 登录到 [Azure Stack 管理门户](https://adminportal.local.azurestack.external)。

2. 单击“市场管理” > “从 Azure 添加”。

    ![](media/asdk-register/2.PNG)

3. 如果看到 Azure 提供的项列表，则表示激活成功。

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>移动注册资源
支持在同一订阅下的资源组之间移动注册资源。 有关将资源移到新的资源组的详细信息，请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。


## <a name="next-steps"></a>后续步骤
[添加 Azure Stack 市场项](.\.\azure-stack-marketplace.md)
