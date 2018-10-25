---
title: 获准对 Azure Front Door 服务启用自定义 HTTPS 的证书颁发机构 | Microsoft Docs
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984760"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>获准对 Azure Front Door 服务启用自定义 HTTPS 的证书颁发机构

对于 Azure Front Door 服务自定义域，如果[使用自己的证书启用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)，必须使用获准的证书颁发机构 (CA) 来创建 SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

创建自己的证书时，以下 CA 获准：

- AddTrust 外部 CA 根
- AlphaSSL 根 CA
- AME 基础结构 CA 01
- AME 基础结构 CA 02
- Ameroot
- AP 根 CA
- AP 根证书颁发机构 2013
- AP 根证书颁发机构 2014
- APCA-DM3P
- Autopilot 根 CA
- Baltimore CyberTrust 根
- 类 3 公共主要证书颁发机构
- COMODO RSA 证书颁发机构
- COMODO RSA 域验证安全服务器 CA
- D-TRUST 根类 3 CA 2 2009
- DigiCert 云服务 CA-1
- DigiCert 全局根 CA
- DigiCert 高保障 CA-3
- DigiCert 高保障 EV 根 CA
- DigiCert SHA2 扩展验证服务器 CA
- DigiCert SHA2 高保障服务器 CA
- DigiCert SHA2 安全服务器 CA
- DST 根 CA X3
- D-TRUST 根类 3 CA 2 2009
- 加密无处不在 DV TLS CA
- 委托根证书颁发机构
- 委托根证书颁发机构 - G2
- Entrust.net 证书颁发机构 (2048)
- GeoTrust 全局 CA
- GeoTrust 主要证书颁发机构
- GeoTrust 主要证书颁发机构 - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign 扩展验证n CA - SHA256 - G2
- GlobalSign 组织验证 CA - G2
- GlobalSign 根 CA
- Go Daddy 根证书颁发机构 - G2
- Go Daddy 安全证书颁发机构 - G2
- RapidSSL RSA CA 2018
- 根代理
- Symantec 类 3 EV SSL CA - G3
- Symantec 类 3 安全服务器 CA - G4
- 适用于 Microsoft 的 Symantec 企业移动根
- Thawte 主要根 CA
- Thawte 主要根 CA - G2
- Thawte 主要根 CA - G3
- Thawte RSA CA 2018
- Thawte 时间戳 CA
- TrustAsia TLS RSA CA
- VeriSign 类 3 扩展验证 SSL CA
- VeriSign 类 3 扩展验证 SSL SGC CA
- VeriSign 类 3 公共主要证书颁发机构 - G5
- VeriSign 国际服务器 CA - 类 3
- VeriSign 时间戳服务根
- VeriSign 通用根证书颁发机构
