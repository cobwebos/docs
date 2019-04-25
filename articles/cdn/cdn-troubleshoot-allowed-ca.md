---
title: 获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构 | Microsoft Docs
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323512"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构

对于“Microsoft 提供的 Azure CDN 标准版”终结点上的 Azure 内容分发网络 (CDN) 自定义域，如果[使用自己的证书启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)，必须使用获准的证书颁发机构 (CA) 来创建 SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

> [!NOTE]
> 使用自己的证书启用自定义 HTTPS 仅适用于“Microsoft 提供的 Azure CDN 标准版”配置文件。 
>

创建自己的证书时，以下 CA 获准：

- AddTrust 外部 CA 根
- AlphaSSL 根 CA
- AME 基础结构 CA 01
- AME 基础结构 CA 02
- Ameroot
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
- QuoVadis 根 CA2 G3
- RapidSSL RSA CA 2018
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


