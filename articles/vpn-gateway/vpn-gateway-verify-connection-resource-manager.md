---
title: "验证网关连接 | Microsoft 文档"
description: "本文说明如何验证 Resource Manager 部署模型中的网关连接"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 420b193d558d00f574d24c87ab9db6e9e757a952


---
# <a name="verify-a-gateway-connection"></a>验证网关连接
可使用几种不同的方法验证网关连接。 本文将演示如何使用 Azure 门户和 PowerShell 验证 Resource Manager 网关连接的状态。

## <a name="verify-using-powershell"></a>使用 PowerShell 验证
你需要安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的信息，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。 有关使用 Resource Manager cmdlet 的详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

### <a name="step-1-log-in-to-your-azure-account"></a>步骤 1：登录到 Azure 帐户
1. 使用提升的权限打开 PowerShell 控制台，然后连接到帐户。
   
        Login-AzureRmAccount
2. 检查该帐户的订阅。
   
        Get-AzureRmSubscription 
3. 指定要使用的订阅。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>步骤 2：验证连接
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>使用 Azure 门户验证
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>后续步骤
* 你可以将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。




<!--HONumber=Nov16_HO3-->


