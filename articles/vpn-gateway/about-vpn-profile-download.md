---
title: Azure VPN 网关：关于 P2S VPN 客户端配置文件
description: 这有助于你使用客户端配置文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528500"
---
# <a name="about-p2s-vpn-client-profiles"></a>关于 P2S VPN 客户端配置文件

下载的配置文件包含配置 VPN 连接所需的信息。 本文将有助于你获取和了解 VPN 客户端配置文件所需的信息。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 文件夹**包含需要修改以包括密钥和证书的 *ovpn* 配置文件。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 网关上选择了 Azure AD 身份验证，则此文件夹不会出现在 zip 文件中。 但是，azurevpnconfig.xml 将位于 AzureVPN 文件夹中。

## <a name="next-steps"></a>后续步骤

有关点到站点的详细信息，请参阅[关于点到站点](point-to-site-about.md)。
