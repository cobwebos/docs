---
title: "修改本地网络网关 IP 地址前缀和网关 IP | Microsoft 文档"
description: "本文介绍如何更改本地网络网关的 IP 地址前缀"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e88511058b1de79ca222d87ada6c9abdf4daf11c


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>使用 PowerShell 修改本地网络网关设置
有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。 以下说明有助于修改本地网络网关设置。 也可在 Azure 门户中修改这些设置。

## <a name="before-you-begin"></a>开始之前
你需要安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 。

## <a name="to-modify-ip-address-prefixes"></a>修改 IP 地址前缀
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>修改网关 IP 地址
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>后续步骤
可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。




<!--HONumber=Nov16_HO3-->


