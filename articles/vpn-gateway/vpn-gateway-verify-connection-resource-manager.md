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
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3


---
# <a name="verify-a-vpn-gateway-connection"></a>验证 VPN 网关连接
可以使用门户和 PowerShell 验证虚拟网络 VPN 网关连接。 本文包含适用于 Resource Manager 与经典部署模型的步骤。

## <a name="verify-using-the-azure-portal"></a>使用 Azure 门户验证

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>使用 PowerShell 验证

若要使用 PowerShell 进行验证，请安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 有关使用 Resource Manager cmdlet 的详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

### <a name="log-in-to-your-azure-account"></a>登录到 Azure 帐户
1. 使用提升的权限打开 PowerShell 控制台，然后连接到帐户。
   
        Login-AzureRmAccount
2. 检查该帐户的订阅。
   
        Get-AzureRmSubscription 
3. 指定要使用的订阅。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>验证连接

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>使用 Azure 门户（经典）验证
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>使用 PowerShell（经典）验证
若要使用 PowerShell 进行验证，请安装最新版本的 Azure PowerShell cmdlet。 请务必同时下载并安装 Resource Manager 和服务管理 (SM) 版本。 有关安装 PowerShell cmdlet 的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 

### <a name="log-in-to-your-azure-account"></a>登录到 Azure 帐户
1. 使用提升的权限打开 PowerShell 控制台，然后连接到帐户。
   
        Login-AzureRmAccount
2. 检查该帐户的订阅。
   
        Get-AzureRmSubscription 
3. 指定要使用的订阅。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. 登录以使用适用于经典部署模型的服务管理 cmdlet。

        Add-AzureAccount

### <a name="verify-your-connection"></a>验证连接
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>后续步骤
* 你可以将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。




<!--HONumber=Jan17_HO5-->


