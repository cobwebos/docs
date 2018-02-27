---
title: "安装 P2S 客户端证书 | Azure"
description: "安装用于 P2S 证书身份验证的 Mac 或 Windows 客户端证书。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: de98201b65f5531f334aded1056f622cecb6e190
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>为点到站点 Azure 证书身份验证连接安装客户端证书

使用点到站点 Azure 证书身份验证连接到虚拟机的所有客户端都需要一个客户端证书。 本文可用来帮助安装在使用 P2S 连接到 VNet 时用于身份验证的客户端证书。

## <a name="generate"></a>生成并导出客户端证书

可以基于使用企业 CA 解决方案生成的根证书来生成客户端证书，也可以生成自签名证书。 有关步骤，请参阅 [PowerShell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 说明。 生成客户端证书后，将其导出为 .pfx 文件。 在导出时，请确保包括整个证书链。

## <a name="installwin"></a>在 Windows 客户端上安装证书

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>在 Mac 客户端上安装证书

Mac VPN 客户端仅在资源管理器部署模型中受支持。 经典部署模型不支持它们。

> [!NOTE]
>  IKEv2 目前以预览版提供。
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>后续步骤

继续执行点到站点配置步骤。

* [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure 门户（经典）](vpn-gateway-howto-point-to-site-classic-azure-portal.md)