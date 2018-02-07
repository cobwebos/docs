---
title: "Azure 注册 Azure 堆栈集成系统 |Microsoft 文档"
description: "介绍 Azure 堆栈 Azure 连接的多节点部署的 Azure 注册过程。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d5b77bb43c48bd286708ca96699b20be0f761baa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="register-azure-stack-with-azure"></a>向 Azure 注册 Azure 堆栈
Azure 堆栈可以注册 Azure 下载从 Azure 应用商店项以及设置回向 Microsoft 报告的商业数据。 注册 Azure 堆栈后，使用情况报告给 Azure 商务。 你可以在用于注册的订阅下看到它。

> [!IMPORTANT]
> 注册是必需的如果你选择为你的使用付费计费模型。 否则，你将会违反 Azure 堆栈部署的许可条款因为使用情况将否则不会报告。

## <a name="before-you-register-azure-stack-with-azure"></a>之前向 Azure 注册 Azure 堆栈
在之前向 Azure 注册 Azure 堆栈，您必须：

- Azure 订阅的订阅 ID。 若要获取的 ID，登录到 Azure，单击**更多的服务** > **订阅**，单击你想要使用，的订阅并在列表视图**Essentials**可以找到订阅 id。 

  > [!NOTE]
  > 当前不支持中国、 德国和我们政府云订阅。 

- 用户名和密码是订阅的所有者的帐户 （MSA/2FA 帐户也支持）
- *不需要从 Azure 堆栈 1712年更新版本 (180106.1) 开始*: Azure 订阅的 Azure AD。 通过将鼠标悬停在 Azure 门户右上角用户标识，可以在 Azure 中找到此目录。 
- 注册 Azure 堆栈资源提供程序 （请参阅下面有关详细信息的注册 Azure 堆栈资源提供程序部分）

如果你没有满足这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure 堆栈会产生任何费用在你的 Azure 订阅。

### <a name="bkmk_powershell"></a>安装适用于 Azure 堆栈 PowerShell
你需要使用 Azure 堆栈的最新 PowerShell 向 Azure 注册系统。

如果尚未安装，[安装适用于 Azure 堆栈 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。 

### <a name="bkmk_tools"></a>下载 Azure 堆栈工具
Azure 堆栈工具 GitHub 存储库包含 PowerShell 模块，支持 Azure 堆栈这样的功能。包括注册功能。 在注册过程过程将需要导入和使用 RegisterWithAzure.psm1 PowerShell 模块中，找到 Azure 堆栈工具储存库中，向 Azure 注册你的 Azure 堆栈实例。 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>在连接的环境中注册 Azure 堆栈
连接的环境可以访问 internet 和 Azure。 对于这些环境中，你需要向 Azure 注册 Azure 堆栈资源提供程序，然后配置你的计费模型。

### <a name="register-the-azure-stack-resource-provider"></a>注册 Azure 堆栈资源提供程序
若要向 Azure 注册 Azure 堆栈资源提供程序，请以管理员身份启动 Powershell ISE，并使用以下 PowerShell 命令。 这些命令将：
- 提示你登录作为使用和设置的 Azure 订阅所有者`EnvironmentName`参数**AzureCloud**。
- 注册 Azure 资源提供程序**Microsoft.AzureStack**。

若要运行的 PowerShell:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Azure 中使用为你的使用付费计费模型注册 Azure 堆栈
使用这些步骤来注册 Azure 堆栈 azure 中使用为你的使用付费计费模型。

以管理员身份启动 PowerShell ISE 并导航到**注册**文件夹中的**AzureStack 工具 master**目录创建时你[下载 Azure 堆栈工具](#bkmk_tools). 导入**RegisterWithAzure.psm1**使用 PowerShell 模块： 

若要运行的 PowerShell:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
接下来，在相同的 PowerShell 会话中，运行**集 AzsRegistration** cmdlet。 提示输入凭据时，指定的 Azure 订阅的所有者。  

若要运行的 PowerShell:

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
|CloudAdminCredential|PowerShell 对象，其中包含 Azure 订阅的所有者的凭据信息 （用户名和密码）。|
|PrivilegedEndpoint|预配置远程 PowerShell 控制台，它提供功能，如日志收集和其他文章部署任务。 若要了解详细信息，请参见[使用特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint)文章。|
|BillingModel|使用你的订阅的计费模型。 允许的值为此参数为： 容量、 PayAsYouUse 和开发。|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Azure 中使用容量计费模型注册 Azure 堆栈
按照相同的说明用于注册使用作为你的使用付费计费模型，但添加在其下购买容量的协议编号，并更改`BillingModel`参数**容量**。 所有其他参数不变。

若要运行的 PowerShell:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>在连接断开的环境中注册 Azure 堆栈 
*此部分中的信息适用 Azure 堆栈 1712年更新版本 (180106.1) 开头，并且不支持与早期版本。*

如果你要在连接断开的环境中注册 Azure 堆栈 （具有没有 internet 连接），则需要从 Azure 堆栈环境中获取了注册令牌，然后可以连接到 Azure 并且具有的计算机上使用该令牌[PowerShell有关安装 Azure 堆栈](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure 堆栈环境中获取了注册令牌
  1. 若要获取注册令牌，请运行以下 PowerShell 命令：  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 注册令牌保存在为指定的文件*$env:SystemDrive\RegistrationToken.txt*。

  2. 保存在 Azure 上使用此注册令牌机的连接。


### <a name="connect-to-azure-and-register"></a>连接到 Azure 以及注册
以管理员身份启动 PowerShell ISE 并导航到**注册**文件夹中的**AzureStack 工具 master**目录创建时你[下载 Azure 堆栈工具](#bkmk_tools). 导入**RegisterWithAzure.psm1**模块： 

若要运行的 PowerShell:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
接下来，在相同的 PowerShell 会话中，指定要向 Azure 注册的注册令牌：

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
（可选） 你可以使用 Get-content cmdlet 点到包含您的注册令牌的文件：

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> 保存注册资源名称或以供将来参考的注册令牌。

## <a name="verify-azure-stack-registration"></a>验证 Azure 堆栈注册
使用以下步骤来验证 Azure 堆栈已成功向 Azure 注册。
1. 登录到 Azure 堆栈[管理员门户](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https &#58; / / adminportal。*&lt;区域 >。&lt;fqdn >*。
2. 单击**更多的服务** > **应用商店管理** > **添加从 Azure**。

如果你看到的项 （如 WordPress) 的 Azure 上提供的列表，已成功激活。

> [!NOTE]
> 完成注册后，将不再显示未注册的活动警告。

## <a name="renew-or-change-registration"></a>续订或更改注册
你将需要更新或续订你的注册出现下列情况：
- 后续订基于容量的每年订阅。
- 当你更改计费模型。
- 当扩展基于容量的计费更改 （添加/删除节点）。

### <a name="change-the-subscription-you-use"></a>更改你使用的订阅
如果你想要更改订阅你使用，必须先运行**删除 AzsRegistration** cmdlet，然后，确保已登录到正确的 Azure PowerShell 上下文中，并最终运行**集 AzsRegistration**与任意更改参数：

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>更改计费模型或联合功能
如果你想要更改计费模型或你安装的联合功能，你可以调用要设置新值的注册函数。 不需要先删除当前的注册： 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>删除已注册的资源
如果你想要删除了注册，则必须使用**注销 AzsEnvironment** cmdlet 并在注册资源名称或注册的传入令牌所用的**注册 AzsEnvironment**.

若要删除注册使用的资源名称：

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
若要删除注册使用注册令牌：

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>后续步骤

[外部的监视集成](azure-stack-integrate-monitor.md)