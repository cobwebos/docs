---
title: 为 P2S 生成和导出证书： PowerShell
titleSuffix: Azure VPN Gateway
description: 在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书、导出公钥和生成客户端证书。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393540"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>使用 PowerShell 为点到站点连接生成和导出证书

点到站点连接使用证书进行身份验证。 本文介绍如何在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书并生成客户端证书。 如果正在查找不同的证书说明，请参阅[证书 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 或[证书 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)。

必须在运行 Windows 10 或 Windows Server 2016 的计算机上执行本文中的步骤。 用于生成证书的 PowerShell cmdlet 是操作系统的一部分，在其他版本的 Windows 上不正常工作。 只需 Windows 10 或 Windows Server 2016 计算机即可生成证书。 生成证书后，可上传证书，或在任何支持的客户端操作系统上安装该证书。

如果无法访问 Windows 10 或 Windows Server 2016 计算机，则可使用 [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) 生成证书。 使用任一方法生成的证书均可安装在[支持的](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)所有客户端操作系统上。

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>安装已导出的客户端证书

通过 P2S 连接连接到 VNet 的每个客户端都需要在本地安装客户端证书。

若要安装客户端证书，请参阅[为点到站点连接安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="next-steps"></a>后续步骤

继续使用点到站点配置。

* 有关**资源管理器**部署模型步骤，请参阅[使用本机 Azure 证书身份验证配置 P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
* 有关**经典**部署模型步骤，请参阅 [Configure a Point-to-Site VPN connection to a VNet (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)（配置与 VNet 的点到站点 VPN 连接（经典））。