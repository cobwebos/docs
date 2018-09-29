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
ms.date: 09/28/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 09f5dbdb173e1613ed942391da7baaeb045654e4
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452524"
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure

将 Azure Stack 注册到 Azure 即可从 Azure 下载市场项，并设置向 Microsoft 报告商业数据的功能。 注册 Azure Stack 之后，使用情况会报告给 Azure 商业组件，然后你就可以在用于注册的订阅下查看它。

在本文中的信息介绍使用 Azure 注册 Azure Stack 集成系统。 有关将 ASDK 注册到 Azure 的信息，请参阅[Azure Stack 注册](.\asdk\asdk-register.md)ASDK 文档中。

> [!IMPORTANT]  
> 需要注册才能支持完整的 Azure Stack 功能，包括在市场中提供商品。 此外，如果你在使用即用即付计费模式时未注册，则将违反 Azure Stack 许可条款。 若要了解有关 Azure Stack 授权模型的详细信息，请参阅[“如何购买”页](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必备组件

在注册之前，需将以下项准备就绪：

 - 验证凭据
 - 设置 PowerShell 语言模式
 - 安装适用于 Azure Stack 的 PowerShell
 - 下载 Azure Stack 工具
 - 确定注册方案

### <a name="verify-your-credentials"></a>验证凭据

将 Azure Stack 注册到 Azure 之前，必须准备好：

- Azure 订阅的订阅 ID。 EA、 CSP 或 CSP 共享的服务注册为支持订阅。 Csp 需要决定是否[使用 CSP 或 CSPSS 订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-cspss-subscription)。<br><br>若要获取的 ID，登录到 Azure 中，单击**所有服务**。 然后，在**常规**类别中，选择**订阅**，单击所需的订阅并在**Essentials**可以找到订阅 id。

  > [!Note]  
  > 当前不支持德国云订阅。

- 用户名和密码的帐户的所有者的订阅。

- 用户帐户必须有权访问 Azure 订阅并且有权在与该订阅相关联的目录中创建标识应用程序和服务主体。

- 已注册 Azure Stack 资源提供程序（请参阅下面的“注册 Azure Stack 资源提供程序”部分以了解详细信息）。

注册后，不需要进行 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，资源提供程序安装程序脚本或需要要向其授予权限的新功能。 你可以暂时恢复该帐户的全局管理员权限或使用单独的全局管理员帐户的所有者*默认提供商订阅*。

如果没有符合这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。

### <a name="powershell-language-mode"></a>PowerShell 语言模式

若要成功注册 Azure Stack，必须将 PowerShell 语言模式设置为 **FullLanguageMode**。  若要验证当前语言模式设置为 full，打开提升的 PowerShell 窗口并运行以下 PowerShell cmdlet:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguageMode**。 如果返回了其他任何语言模式，则需要在另一台计算机上运行注册，或者将语言模式设置为 **FullLanguageMode**，然后才能继续。

### <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

需要使用适用于 Azure Stack 的最新 PowerShell 来注册到 Azure。

如果最新版本尚未安装，请参阅[安装适用于 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。

### <a name="download-the-azure-stack-tools"></a>下载 Azure Stack 工具

Azure Stack 工具 GitHub 存储库包含支持 Azure Stack 功能（包括注册功能）的 PowerShell 模块。 在注册过程中，需要导入并使用 Azure Stack 工具存储库中的 **RegisterWithAzure.psm1** PowerShell 模块，将 Azure Stack 实例注册到 Azure。

若要确保使用最新版本，应在注册到 Azure 之前删除 Azure Stack 工具的任何现有版本，然后[从 GitHub 下载最新版本](azure-stack-powershell-download.md)。

### <a name="determine-your-registration-scenario"></a>确定注册方案

Azure Stack 部署可能处于“已连接”或“已断开连接”状态。

 - **已连接**  
 “已连接”意味着 Azure Stack 已部署，因此可以连接到 Internet 和 Azure。 可以将 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 用于标识存储。 对于已连接的部署，可供选择的有两种计费模型：即用即付或基于容量。
    - [使用**即用即付**计费模型将连接的 Azure Stack 注册到 Azure](#register-connected-with-pay-as-you-go-billing)
    - [使用**容量**计费模型将连接的 Azure Stack 注册到 Azure](#register-connected-with-capacity-billing)

 - **已断开连接**  
 使用从 Azure 部署断开连接选项，可以在没有 Internet 连接的情况下部署和使用 Azure Stack。 但是，使用断开连接部署，你将受限于一个 AD FS 标识存储和基于容量的计费模型。
    - [断开连接的 Azure Stack 使用注册**容量**计费模型 ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>确定要使用的唯一注册名称 
时将 Azure Stack 注册到 Azure 时，必须提供唯一的注册名称。 若要将 Azure Stack 订阅与 Azure 注册相关联的简单方法是使用 Azure Stack**的云 ID**。 

> [!NOTE]
> 使用基于容量的计费模型的 azure Stack 注册将需要重新注册这些每年的订阅过期后时更改的唯一名称。

若要确定 Azure Stack 部署的云 ID，先打开 PowerShell，在计算机上的管理员不是可以访问特权终结点，运行以下命令，并记录**CloudID**值： 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>使用即用即付计费模型注册连接的 Azure Stack

执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

1. 若要将 Azure Stack 资源提供程序注册到 Azure，以管理员身份启动 PowerShell ISE，并使用以下 PowerShell cmdlet 并结合**EnvironmentName**参数设置为相应的 Azure 订阅类型 （请参阅下面的参数）。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示输入 Azure 帐户凭据。可能必须使用双重身份验证，具体取决于帐户的配置。

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 受支持的环境名称**AzureCloud**， **AzureUSGovernment**，或使用中国 Azure 订阅，如果**AzureChinaCloud**。  |

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 接下来，在同一个 PowerShell 会话中，确保已登录到正确的 Azure PowerShell 上下文。 这是用于注册上述 Azure Stack 资源提供程序的 Azure 帐户。 要运行的 Powershell：

   ```PowerShell  
   Add-AzureRmAccount -Environment "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

7. 在同一个 PowerShell 会话中运行 **Set-AzsRegistration** cmdlet。 要运行的 PowerShell：  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   有关 Set-AzsRegistration cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。

  该过程需要花费 10 到 15 分钟。 命令完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

## <a name="register-connected-with-capacity-billing"></a>使用容量计费模型注册连接的 Azure Stack

执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

1. 若要将 Azure Stack 资源提供程序注册到 Azure，以管理员身份启动 PowerShell ISE，并使用以下 PowerShell cmdlet 并结合**EnvironmentName**参数设置为相应的 Azure 订阅类型 （请参阅下面的参数）。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示输入 Azure 帐户凭据。可能必须使用双重身份验证，具体取决于帐户的配置。

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 受支持的环境名称**AzureCloud**， **AzureUSGovernment**，或使用中国 Azure 订阅，如果**AzureChinaCloud**。  |

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > 可以通过 **Set-AzsRegistration** cmdlet 的 UsageReportingEnabled 参数禁用使用情况报告。 将此参数设置为 false。 例如：`UsageReportingEnabled
   
  有关 Set-AzsRegistration cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。

## <a name="register-disconnected-with-capacity-billing"></a>使用容量计费模型注册断开连接的 Azure Stack

若要在离线环境（未建立 Internet 连接）中注册 Azure Stack，需要从 Azure Stack 环境获取注册令牌，然后在可连接到 Azure 并已[安装适用于 Azure Stack 的 PowerShell](#bkmk_powershell) 的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌

1. 以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 若要获取注册令牌，请运行以下 PowerShell cmdlet:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   有关 Get-AzsRegistrationToken cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。

   > [!Tip]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

3. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 $FilePathForRegistrationToken 复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接到 Internet 的计算机上，执行相同的步骤以导入 RegisterWithAzure.psm1 模块，并登录到正确的 Azure Powershell 上下文。 然后调用 Register-AzsEnvironment。 指定可注册到 Azure 的注册令牌。 如果使用同一 Azure 订阅 ID 注册多个 Azure Stack 实例，请指定唯一的注册名称。 运行以下 cmdlet：

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

（可选）可以使用 Get-Content cmdlet，使之指向包含注册令牌的文件：

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > 保存注册资源名称和注册令牌，供以后参考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

接下来，需要在执行 Register-AzsEnvironment 期间，从 Azure 中创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell cmdlet:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > 激活密钥保存在为 *$KeyOutputFilePath* 指定的文件中。 可以自行更改文件路径或文件名。

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

使用 Get-AzsActivationKey 从创建的激活密钥中获取文件或文本后，返回到 Azure Stack 环境。 接下来，使用该激活密钥在 Azure Stack 中创建激活资源。 若要创建激活资源，请运行以下 PowerShell cmdlet:  

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

使用以下步骤来验证 Azure Stack 是否已成功注册到 Azure。

1. 登录到 Azure Stack [管理员门户](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal)：https&#58;//adminportal.*&lt;区域>.&lt;fqdn>*。
2. 选择**所有服务**，然后在**管理**类别中，选择**Marketplace 管理** > **从 Azure添加**.

如果看到 Azure 提供的项列表（例如 WordPress），则表示激活成功。 但是，在离线环境中，Azure Stack 市场不会显示 Azure 市场项。

> [!Note]  
> 完成注册后，将不再显示提示未注册的活动警告。

## <a name="renew-or-change-registration"></a>续订或更改注册

### <a name="renew-or-change-registration-in-connected-environments"></a>在联网环境中续订或更改注册

在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="change-the-subscription-you-use"></a>更改使用的订阅

若要更改使用的订阅，必须先运行 **Remove-AzsRegistration** cmdlet，然后确保登录到正确的 Azure PowerShell 上下文，最后使用任何已更改的参数来运行 **Set-AzsRegistration**：

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>更改计费模型或功能提供方式

若要更改安装的计费模型或功能提供方式，可以调用注册函数来设置新值。 不需要先删除当前注册：

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>在离线环境中续订或更改注册

在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>从 Azure Stack 中删除激活资源

首先需要从 Azure Stack 中删除激活资源，然后再删除 Azure 中的注册资源。  

若要在 Azure Stack 中删除激活资源，请在 Azure Stack 环境中运行以下 PowerShell cmdlet:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

接下来，若要在 Azure 中删除注册资源，请确保位于 Azure 连接计算机，登录到正确的 Azure PowerShell 上下文，并运行相应的 PowerShell cmdlet，如下所述。

可以使用用于创建资源的注册令牌：  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

或者，可以使用注册名称：

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>使用适用于联网场景的步骤重新注册

现已在离线场景中完全取消注册，接下来必须重复上述步骤，在离线场景中注册 Azure Stack 环境。

### <a name="disable-or-enable-usage-reporting"></a>禁用或启用使用情况报告

对于使用容量计费模型的 Azure Stack 环境，将使用情况报告与关闭**UsageReportingEnabled**使用的参数**Set-azsregistration**或**Get AzsRegistrationToken** cmdlet。 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。

#### <a name="with-a-connected-azure-stack"></a>使用连接的 Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>使用断开连接的 Azure Stack

1. 若要更改注册令牌，请运行以下 PowerShell cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

2. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 $FilePathForRegistrationToken 复制文件或文本。

## <a name="move-a-registration-resource"></a>移动注册资源
在同一订阅下的资源组之间移动注册资源**是**支持对所有环境。 但是，订阅之间移动注册资源时才支持的 Csp 订阅这两个解析为相同的合作伙伴 id。 有关将资源移到新的资源组的详细信息，请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

## <a name="registration-reference"></a>注册参考

### <a name="set-azsregistration"></a>Set-AzsRegistration

可以使用 Set-AzsRegistration 将 Azure Stack 注册到 Azure，并启用或禁用在市场中提供商品的功能和使用情况报告功能。

若要运行此 cmdlet，需具备：
- 任何类型的全球 Azure 订阅。
- 此外还必须在登录到 Azure PowerShell 时，使用其身份为该订阅的所有者或参与者的帐户。

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| 参数 | Type | 说明 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | String | 预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模型。 此参数允许的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled |  |  |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数的允许值：True、False。 |
| AgreementNumber | String |  |
| RegistrationName | String | 如果在多个使用同一 Azure 订阅 ID 的 Azure Stack 实例上运行注册脚本，请为注册设置唯一名称。 参数的默认值为 **AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用同一名称，脚本会失败。 |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken 会根据输入参数生成注册令牌。

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| 参数 | Type | 说明 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | String |  预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模型。 此参数允许的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数的允许值：True、False。 |
| AgreementNumber | String |  |


## <a name="next-steps"></a>后续步骤

[从 Azure 下载市场项](azure-stack-download-azure-marketplace-item.md)
