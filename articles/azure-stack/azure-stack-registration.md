---
title: Azure Stack 集成系统的 Azure 注册 | Microsoft Docs
description: 介绍多节点 Azure Stack Azure 连接部署的 Azure 注册过程。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 7d14b246220264641a3bb726d5505c25dc25bbbd
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248136"
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure

将 [Azure Stack](azure-stack-poc.md) 注册到 Azure 即可从 Azure 下载 Marketplace 项，并设置向 Microsoft 报告商业数据的功能。 注册 Azure Stack 之后，使用情况会报告给 Azure 商业组件，然后你就可以在用于注册的订阅下查看它。

> [!IMPORTANT]
> 需要注册才能支持完整的 Azure Stack 功能，包括 Marketplace 联合。 此外，如果你在使用即用即付计费模式时未注册，则将违反 Azure Stack 许可条款。 若要了解有关 Azure Stack 授权模型的详细信息，请参阅[“如何购买”页](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必备组件

将 Azure Stack 注册到 Azure 之前，必须准备好：

- Azure 订阅的订阅 ID。 若要获取该 ID，请登录到 Azure，单击“更多服务” > “订阅”，单击要使用的订阅，然后，在“概要”下可以找到订阅 ID。

  > [!NOTE]
  > 当前不支持德国和美国政府云订阅。

- 订阅所有者的帐户用户名和密码（支持 MSA/2FA 帐户）。
- 已注册 Azure Stack 资源提供程序（请参阅下面的“注册 Azure Stack 资源提供程序”部分以了解详细信息）。

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。

### <a name="powershell-language-mode"></a>PowerShell 语言模式

若要成功注册 Azure 堆栈，PowerShell 语言模式必须设置为**FullLanguageMode**。  若要验证的当前语言模式设置为完全，打开提升的 PowerShell 窗口并运行以下 PowerShell 命令：

```powershell
$ExecutionContext.SessionState.LanguageMode
```

确保输出将返回**FullLanguageMode**。 如果返回其他任何语言模式、 注册将需要在另一台计算机上运行或语言模式将需要设置为**FullLanguageMode**才能继续。

### <a name="bkmk_powershell"></a>安装适用于 Azure Stack 的 PowerShell

需要使用适用于 Azure Stack 的最新 PowerShell 来注册到 Azure。

[安装适用于 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)（如果尚未安装）。

### <a name="bkmk_tools"></a>下载 Azure Stack 工具

Azure Stack 工具 GitHub 存储库包含支持 Azure Stack 功能（包括注册功能）的 PowerShell 模块。 在注册过程中，需要导入并使用 Azure Stack 工具存储库中的 RegisterWithAzure.psm1 PowerShell 模块，将 Azure Stack 实例注册到 Azure。

若要确保使用最新版本，应在注册到 Azure 之前删除 Azure Stack 工具的任何现有版本，然后[从 GitHub 下载最新版本](azure-stack-powershell-download.md)。

## <a name="register-azure-stack-in-connected-environments"></a>在连接的环境中注册 Azure Stack

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

> [!NOTE]
> 所有这些步骤必须在可以访问特权终结点的计算机上运行。

### <a name="register-the-azure-stack-resource-provider"></a>注册 Azure Stack 资源提供程序

若要向 Azure 注册 Azure Stack 资源提供程序，请以管理员身份启动 PowerShell ISE，然后使用以下 PowerShell 命令，并将 **EnvironmentName** 参数设置为相应的 Azure 订阅类型（请参阅下面的“参数”）。

1. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示输入 Azure 全局管理员帐户凭据。可能必须使用双重身份验证，具体取决于帐户的配置。

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为 **AzureCloud**；如果使用中国 Azure 订阅，则环境名称为 **AzureChinaCloud**。  |
   |  |  |

2. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>使用即用即付计费模式将 Azure Stack 注册到 Azure

执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

1. 以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 接下来，在同一个 PowerShell 会话中，确保已登录到正确的 Azure PowerShell 上下文。 这是用于注册上述 Azure Stack 资源提供程序的 Azure 帐户。 要运行的 Powershell：

   ```powershell
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. 在同一个 PowerShell 会话中运行 **Set-AzsRegistration** cmdlet。 要运行的 PowerShell：  

   ```powershell
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
   ```

  |参数|说明|
  |-----|-----|
  |PrivilegedEndpointCredential|用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。|
  |PrivilegedEndpoint|预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。|
  |BillingModel|订阅使用的计费模式。 此参数允许的值：Capacity、PayAsYouUse 和 Development。|
  |  |  |

  该过程需要花费 10 到 15 分钟。 命令完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>使用容量计费模式将 Azure Stack 注册到 Azure

遵照使用即用即付计费模式进行注册的相同说明，但需要添加购买容量时所依据的协议编号，并将 **BillingModel** 参数更改为 **Capacity**。 其他所有参数不变。

要运行的 PowerShell：

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>在离线环境中注册 Azure Stack

*本部分中的信息适用于 Azure Stack 1712 更新版 (180106.1) 和更高版本，不支持更低的版本。*

若要在离线环境（未建立 Internet 连接）中注册 Azure Stack，需要从 Azure Stack 环境获取注册令牌，然后在可连接到 Azure 并已[安装适用于 Azure Stack 的 PowerShell](#bkmk_powershell) 的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌

1. 以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 若要获取注册令牌，请运行以下 PowerShell 命令：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

3. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 $FilePathForRegistrationToken 复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接到 Internet 的计算机上，执行相同的步骤以导入 RegisterWithAzure.psm1 模块，并登录到正确的 Azure Powershell 上下文。 然后调用 Register-AzsEnvironment，并指定用于在 Azure 中注册的注册令牌：

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

（可选）可以使用 Get-Content cmdlet，使之指向包含注册令牌的文件：

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

  > [!NOTE]  
  > 保存注册资源名称和注册令牌，供以后参考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

接下来，需要在执行 Register-AzsEnvironment 期间，从 Azure 中创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell 命令：  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > 激活密钥保存在为 *$KeyOutputFilePath* 指定的文件中。 可以自行更改文件路径或文件名。

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

使用 Get-AzsActivationKey 从创建的激活密钥中获取文件或文本后，返回到 Azure Stack 环境。 接下来，使用该激活密钥在 Azure Stack 中创建激活资源。 若要创建激活资源，请运行以下 PowerShell 命令：  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

（可选）可以使用 Get-Content cmdlet，使之指向包含注册令牌的文件：

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>验证 Azure Stack 注册

使用以下步骤来验证 Azure 堆栈成功向 Azure 注册。

1. 登录到 Azure Stack [管理员门户](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal)：https&#58;//adminportal.*&lt;区域>.&lt;fqdn>*。
2. 选择**更多的服务** > **应用商店管理** > **添加从 Azure**。

如果看到 Azure 提供的项列表（例如 WordPress），则表示激活成功。 但是，在离线环境中，Azure Stack Marketplace 不会显示 Azure Marketplace 项。

> [!NOTE]
> 完成注册后，将不再显示提示未注册的活动警告。

## <a name="renew-or-change-registration"></a>续订或更改注册

### <a name="renew-or-change-registration-in-connected-environments"></a>在联网环境中续订或更改注册

在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="change-the-subscription-you-use"></a>更改使用的订阅

若要更改使用的订阅，必须先运行 **Remove-AzsRegistration** cmdlet，然后确保登录到正确的 Azure PowerShell 上下文，最后使用任何已更改的参数来运行 **Set-AzsRegistration**：

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>更改计费模式或联合功能

若要更改安装的计费模式或联合功能，可以调用注册函数来设置新值。 不需要先删除当前注册：

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>在离线环境中续订或更改注册

在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>从 Azure Stack 中删除激活资源

首先需要从 Azure Stack 中删除激活资源，然后再删除 Azure 中的注册资源。  

若要删除 Azure Stack 中的激活资源，请在 Azure Stack 环境中运行以下 PowerShell 命令：  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

接下来，若要删除 Azure 中的注册资源，请确保计算机已连接到 Azure，登录到正确的 Azure PowerShell 上下文，并运行相应的 PowerShell 命令，如下所示。

可以使用用于创建资源的注册令牌：  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

或者，可以使用注册名称：

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>使用适用于联网场景的步骤重新注册

现已在离线场景中完全取消注册，接下来必须重复上述步骤，在离线场景中注册 Azure Stack 环境。

## <a name="next-steps"></a>后续步骤

[从 Azure 下载 Marketplace 项](azure-stack-download-azure-marketplace-item.md)
