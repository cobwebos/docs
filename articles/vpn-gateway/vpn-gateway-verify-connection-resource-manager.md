---
title: "验证 VPN 网关连接 | Microsoft 文档"
description: "本文介绍如何验证虚拟网络 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a67ea630cc8b5a3e9deab7733aa5cd2055949ec0
ms.lasthandoff: 04/27/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>验证 VPN 网关连接

本文介绍如何验证 Resource Manager 和经典部署模型的 VPN 网关连接。

## <a name="verify-using-the-azure-portal"></a>使用 Azure 门户验证

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>使用 PowerShell 验证

若要使用 PowerShell 进行验证，请安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 有关使用 Resource Manager cmdlet 的详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

### <a name="log-in-to-your-azure-account"></a>登录到 Azure 帐户
1. 使用提升的权限打开 PowerShell 控制台，然后连接到帐户。

  ```powershell
  Login-AzureRmAccount
  ```
2. 检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. 指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>验证连接

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>使用 Azure CLI 进行验证

若要使用 Azure CLI 进行验证，请安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅 [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（安装 Azure CLI 2.0）。

### <a name="log-in-to-your-azure-account"></a>登录到 Azure 帐户

1. 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

  ```azurecli
  az login
  ```
2. 如果有多个 Azure 订阅，请列出该帐户的订阅。

  ```azurecli
  Az account list --all
  ```
3. 指定要使用的订阅。

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>验证连接

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>使用 Azure 门户（经典）验证
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>使用 PowerShell（经典）验证
若要使用 PowerShell 进行验证，请安装最新版本的 Azure PowerShell cmdlet。 请务必同时下载并安装 Resource Manager 和服务管理 (SM) 版本。 有关安装 PowerShell cmdlet 的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 

### <a name="log-in-to-your-azure-account"></a>登录到 Azure 帐户
1. 使用提升的权限打开 PowerShell 控制台，然后连接到帐户。

  ```powershell
  Login-AzureRmAccount
  ```
2. 检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription 
  ```
3. 指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. 登录以使用适用于经典部署模型的服务管理 cmdlet。

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>验证连接
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>后续步骤
* 你可以将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。


