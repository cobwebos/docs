---
title: Azure VPN 网关：关于 P2S VPN 客户端配置文件
description: 使用本文查找 VPN 客户端配置文件所需的信息。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985923"
---
# <a name="about-p2s-vpn-client-profiles"></a>关于 P2S VPN 客户端配置文件

已下载的配置文件包含配置 VPN 连接所需的信息。 本文将帮助你获取和了解 VPN 客户端配置文件所需的信息。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* OpenVPN 文件夹包含 ovpn 配置文件，需要对该文件进行修改以包含密钥和证书。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 网关上选择 Azure AD 身份验证，则 zip 文件中不存在此文件夹。 而是应该导航到 AzureVPN 文件夹并找到 azurevpnconfig.xml。

## <a name="next-steps"></a>后续步骤

有关点到站点的详细信息，请参阅[关于点到站点](point-to-site-about.md)。
