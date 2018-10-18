---
title: 安装点到站点客户端证书：Azure | Microsoft Docs
description: 安装用于 P2S 证书身份验证的客户端证书 - Windows、Mac、Linux。
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: eec15b84e4bdb8df3fe84a53909d5da4b39545ff
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294436"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>安装用于 P2S 证书身份验证连接的客户端证书

使用点到站点 Azure 证书身份验证连接到虚拟机的所有客户端都需要一个客户端证书。 本文可用来帮助安装在使用 P2S 连接到 VNet 时用于身份验证的客户端证书。

## <a name="generate"></a>获取客户端证书

无论要从什么客户端操作系统进行连接，都必须具有客户端证书。 可以基于使用企业 CA 解决方案生成的根证书来生成客户端证书，也可以生成自签名证书。 有关生成客户端证书的步骤，请参阅 [PowerShell](vpn-gateway-certificates-point-to-site.md)、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 或 [Linux](vpn-gateway-certificates-point-to-site-linux.md) 说明。 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 客户端仅在资源管理器部署模型中受支持。 经典部署模型不支持它们。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Linux 客户端证书作为客户端配置的一部分安装在客户端上。 有关说明,请参阅[客户端配置 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。

## <a name="next-steps"></a>后续步骤

继续执行点到站点配置步骤来[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。