---
title: 如何为 Azure VPN 网关配置 OpenVPN 客户端 |Microsoft Docs
description: 为 Azure VPN 网关配置 OpenVPN 客户端的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066106"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>为 Azure VPN 网关配置 OpenVPN 客户端

本文可帮助你配置 OpenVPN  **协议&reg;** 客户端。

## <a name="before-you-begin"></a>开始之前

验证你已完成为 VPN 网关配置 OpenVPN 的步骤。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN](vpn-gateway-howto-openvpn.md)。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>后续步骤

如果希望 VPN 客户端能够访问另一个 VNet 中的资源，请遵照 [VNet 到 VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 文章中的说明设置 VNet 到 VNet 连接。 确保在网关和连接中启用 BGP，否则流量不会流动。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
