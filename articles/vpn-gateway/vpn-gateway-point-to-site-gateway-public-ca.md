---
title: 为 P2S 网关转换到公共 CA 证书 | Azure VPN 网关 | Microsoft Docs
description: 本文可帮助你成功转换 P2S 网关的新公共 CA 证书。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: cherylmc
ms.openlocfilehash: 24956dd51ef4c2544ce28005fa3bff31113e5959
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848912"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>为 P2S 转换到公共 CA 网关证书

建立 P2S 连接时，Azure VPN 网关不再向网关颁发 Azure 级自签名证书。 颁发的证书现在由公共证书颁发机构 (CA) 签名。 但是，部分早期网关可能仍在使用自签名证书。 这些自签名证书即将到达其过期日期，必须转换为公共 CA 证书。

>[!NOTE]
> * 用于 P2S 客户端身份验证的自签名证书不受此 Azure 级证书更改的影响。 可以继续照常颁发和使用自签名证书。
>

本上下文中的证书都是额外的 Azure 级证书。 它们不是生成用于身份验证的自己的自签名根证书和客户端证书时所使用的证书链。 这些证书不受影响，并且将在你为进行身份验证而生成这些证书时过期。

以前，需要每隔 18 个月更新网关的自签名证书（由 Azure 在后台颁发）。 然后，必须生成 VPN 客户端配置文件并将其重新部署到所有 P2S 客户端。 改用公共 CA 证书可消除此限制。 除了转换证书以外，此项更改还能改善平台、指标和稳定性。

只有早期的网关才受此项更改的影响。 如果网关证书需要转换，Azure 门户中会显示通知或 toast 消息。 可以使用本文中的步骤检查你的网关是否受到影响。

> [!IMPORTANT]
> 转换计划为 2019 年 3 月 12 日 18:00 (UTC) 开始。 如需在不同的时间进行转换，可以创建支持案例。 请至少提前 24 个小时提出并确认请求。  可以请求以下时间之一：
>
> * 2 月 25 日 06:00 (UTC)
> * 2 月 25 日 18:00 (UTC)
> * 3 月 1 日 06:00 (UTC)
> * 3 月 1 日 18:00 (UTC)
>
> **所有剩余网关将在 2019 年 3 月 12 日 18:00 (UTC) 开始转换**。
>
> 网关完成转换过程时，客户将收到电子邮件。
> 

## <a name="1-verify-your-certificate"></a>1.验证证书

### <a name="resource-manager"></a>资源管理器

1. 检查你是否受到此项更新的影响。 使用[此文](point-to-site-vpn-client-configuration-azure-cert.md)中的步骤下载当前 VPN 客户端配置。

2. 打开或解压缩 zip 文件，并浏览到“Generic”文件夹。 Generic 文件夹中包含两个文件，其中一个是 *VPNSettings.xml*。
3. 在任何 XML 查看器/编辑器中打开 *VPNSettings.xml*。 在该 XML 文件中搜索以下字段：

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. 如果 ServerCertRotCn 和 ServerCertIssuerCn 是“DigiCert Global Root CA”，则你不受此项更新的影响，无需继续执行本文中的步骤。 但如果它们显示其他内容，则表示你的网关证书需要更新和转换。

### <a name="classic"></a>经典

1. 在客户端计算机上，导航到路径 %appdata%/Microsoft/Network/Connections/Cm/<gatewayID>。 在网关 ID 文件夹中，可以查看证书。
2. 在证书的常规选项卡上，验证颁发机构是否是"DigiCert 全局根 CA"。 如果颁发机构并非此机构，则网关证书将作为更新的一部分进行转换。

## <a name="2-check-certificate-transition-schedule"></a>2.检查证书转换计划

如果你的证书需要更新，则会转换网关证书。 请参阅有关转换时间的**重要说明**。 更新后，P2S 客户端无法使用其旧配置文件进行连接。 必须生成新的 VPN 客户端配置文件，并将其安装在客户端上。

## <a name="3-generate-vpn-client-configuration-profile"></a>3.生成 VPN 客户端配置文件

转换证书后，从 Azure 门户下载新的 VPN 配置文件（VPN 客户端配置文件）。 有关步骤，请参阅[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。 不需要生成新的客户端证书。

## <a name="4-deploy-vpn-client-profile"></a>4.部署 VPN 客户端配置文件

将新的配置文件部署到所有点到站点 VPN 客户端。 在下载用于点到站点连接的新 VPN 配置文件并将其部署到客户端设备之前，VPN 客户端会断开连接。 已在 VPN 客户端计算机上安装的客户端证书无需重新颁发。

## <a name="5-connect-the-vpn-client"></a>5.连接 VPN 客户端

像平时一样从 VPN 客户端连接到 Azure。
