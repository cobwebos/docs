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
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: e51a15b197e875c35997cfe2ac96d673c01a80f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure
注册[Azure 堆栈](azure-stack-poc.md)与 Azure 允许你从 Azure 下载应用商店项并设置回向 Microsoft 报告的商业数据。 注册 Azure 堆栈后，使用情况报告给 Azure 商务和你可以在用于注册的订阅下看到它。 

> [!IMPORTANT]
> 如果选择即用即付计费模式，则必须注册。 否则，将违反 Azure Stack 部署的许可条款，并且不会报告用量信息。

## <a name="prerequisites"></a>必备组件
将 Azure Stack 注册到 Azure 之前，必须准备好：

- Azure 订阅的订阅 ID。 若要获取该 ID，请登录到 Azure，单击“更多服务” > “订阅”，单击要使用的订阅，然后，在“概要”下可以找到订阅 ID。 

  > [!NOTE]
  > 当前不支持中国、 德国和美国政府云订阅。 

- 订阅所有者的帐户用户名和密码（支持 MSA/2FA 帐户）
- 注册 Azure 堆栈资源提供程序 （请参阅下面有关详细信息的注册 Azure 堆栈资源提供程序部分）。

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。

### <a name="bkmk_powershell"></a>安装适用于 Azure Stack 的 PowerShell
你需要使用 Azure 堆栈的最新 PowerShell 将注册 Azure。

[安装适用于 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)（如果尚未安装）。 

### <a name="bkmk_tools"></a>下载 Azure Stack 工具
Azure Stack 工具 GitHub 存储库包含支持 Azure Stack 功能（包括注册功能）的 PowerShell 模块。 在注册过程中，需要导入并使用 Azure Stack 工具存储库中的 RegisterWithAzure.psm1 PowerShell 模块，将 Azure Stack 实例注册到 Azure。 

若要确保使用最新版本，你应删除任何现有版本的 Azure 堆栈工具和[从 GitHub 下载最新版本](azure-stack-powershell-download.md)之前注册 Azure。

## <a name="register-azure-stack-in-connected-environments"></a>在连接的环境中注册 Azure Stack
连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

> [!NOTE]
> 必须有权访问特权终结点的计算机从运行所有这些步骤。 

### <a name="register-the-azure-stack-resource-provider"></a>注册 Azure Stack 资源提供程序
若要将 Azure Stack 资源提供程序注册到 Azure，请以管理员身份启动 Powershell ISE 并使用以下 PowerShell 命令。 这些命令将会：
- 提示你登录作为使用和设置的 Azure 订阅所有者`EnvironmentName`参数**AzureCloud**。
- 注册 Azure 资源提供程序 **Microsoft.AzureStack**。

1. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加的帐户，运行**添加 AzureRmAccount** cmdlet。 系统将提示你输入你的 Azure 全局管理员帐户凭据，并且你可能必须为使用双因素身份验证，具体情况视你的帐户的配置。

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>使用即用即付计费模式将 Azure Stack 注册到 Azure
执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块： 

要运行的 PowerShell：

```powershell
Import-Module .\RegisterWithAzure.psm1
```
接下来，在同一个 PowerShell 会话中运行 **Set-AzsRegistration** cmdlet。 当系统提示输入凭据时，请指定 Azure 订阅的所有者。  

要运行的 PowerShell：

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|参数|说明|
|-----|-----|
|CloudAdminCredential|PowerShell 对象，其中包含 Azure 订阅所有者的凭据信息（用户名和密码）。|
|PrivilegedEndpoint|预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint)一文。|
|BillingModel|订阅使用的计费模式。 此参数允许的值：Capacity、PayAsYouUse 和 Development。|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>使用容量计费模式将 Azure Stack 注册到 Azure
遵照使用即用即付计费模式进行注册的相同说明，但需要添加购买容量时所依据的协议编号，并将 `BillingModel` 参数更改为 **Capacity**。 其他所有参数不变。

要运行的 PowerShell：
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>在断开连接的环境中注册 Azure Stack 
*本部分中的信息适用于 Azure Stack 1712 更新版 (180106.1) 和更高版本，不支持更低的版本。*

若要在断开连接的环境（未建立 Internet 连接）中注册 Azure Stack，需要从 Azure Stack 环境获取注册令牌，然后在可连接到 Azure 并已[安装适用于 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install) 的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌
  1. 若要获取注册令牌，请运行以下 PowerShell 命令：  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 注册令牌保存在针对 *$env:SystemDrive\RegistrationToken.txt* 指定的文件中。

  2. 保存此注册令牌，以便在连接 Azure 的计算机上使用。


### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册
以管理员身份启动 PowerShell ISE，并导航到[下载 Azure Stack 工具](#bkmk_tools)时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 导入 **RegisterWithAzure.psm1** 模块： 

要运行的 PowerShell：
```powershell
Import-Module .\RegisterWithAzure.psm1
```
接下来，在同一个 PowerShell 会话中，指定注册到 Azure 时所用的注册令牌：

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
（可选）可以使用 Get-Content cmdlet 指向包含注册令牌的文件：

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> 保存注册资源名称或注册令牌，供以后参考。

## <a name="verify-azure-stack-registration"></a>验证 Azure Stack 注册
使用以下步骤来验证 Azure Stack 是否已成功注册到 Azure。
1. 登录到 Azure Stack [管理员门户](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal)：https&#58;//adminportal.*&lt;区域>.&lt;fqdn>*。
2. 单击“更多服务” > “Marketplace 管理” > “从 Azure 添加”。

如果看到 Azure 提供的项列表（例如 WordPress），则表示激活成功。

> [!NOTE]
> 完成注册后，将不再显示有关未注册的活动警告。

## <a name="renew-or-change-registration"></a>续订或更改注册
在以下情况下，需要更新或续订注册：
- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

### <a name="change-the-subscription-you-use"></a>更改使用的订阅
若要更改使用的订阅，必须先运行 **Remove-AzsRegistration** cmdlet，然后确保登录到正确的 Azure PowerShell 上下文，最后使用任何已更改的参数来运行 **Set-AzsRegistration**：

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>更改计费模式或联合功能
若要更改安装的计费模式或联合功能，可以调用注册函数来设置新值。 不需要先删除当前注册： 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```
## <a name="next-steps"></a>后续步骤

[从 Azure 下载应用商店项](azure-stack-download-azure-marketplace-item.md)