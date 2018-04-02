---
title: 包含适用于 Azure DNS 的 Azure PowerShell 的文件
description: 包含适用于 Azure DNS 的 Azure PowerShell 的文件
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 1ddfd1ae8dffbc5d381773ea9679713e93a44a32
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>设置适用于 Azure DNS 的 Azure PowerShell

### <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认你具备以下项。

* Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 需安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

此外，若要使用专用区域（公共预览版），需确保具有以下 PowerShell 模块和版本。 
* AzureRM.Dns - [版本 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) 或更高版本
* AzureRM.Network - [版本 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) 或更高版本

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
上述命令的输出需要显示 AzureRM.Dns 的版本为 4.1.0 或以上，AzureRM.Network 的版本为 5.4.0 或以上。  

如果系统中包含更低的版本，可以安装最新版本的 Azure PowerShell，或者使用“模块版本”旁边的链接，从 PowerShell 库下载并安装上述模块。 然后，可以使用以下命令安装这些模块。 这两个模块都是必需的，它们完全可向后兼容。 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

打开 PowerShell 控制台并连接到帐户。 有关详细信息，请参阅[将 PowerShell 与 Resource Manager 配合使用](../articles/azure-resource-manager/powershell-azure-resource-manager.md)。

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>选择订阅
 
检查该帐户的订阅。

```powershell
Get-AzureRmSubscription
```

选择要使用的 Azure 订阅。

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>注册资源提供程序

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。 使用 Azure DNS 前，必须将 Azure 订阅注册为使用此资源提供程序。 对每个订阅而言，这都是一次性操作。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
