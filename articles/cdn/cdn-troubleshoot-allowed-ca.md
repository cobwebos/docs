---
title: 获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构 | Microsoft Docs
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35237044"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构

对于“Microsoft 提供的 Azure CDN 标准版”终结点上的 Azure 内容分发网络 (CDN) 自定义域，如果[使用自己的证书启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)，必须使用获准的证书颁发机构 (CA) 来创建 SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

> [!NOTE]
> 使用自己的证书启用自定义 HTTPS 仅适用于“Microsoft 提供的 Azure CDN 标准版”配置文件。 
>

创建自己的证书时，以下 CA 获准：

- AddTrust 外部 CA 根
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
- DigiCert SHA2 高保障服务器 CA
- DigiCert SHA2 安全服务器 CA
- GlobalSign
- GlobalSign 扩展验证n CA - SHA256 - G2
- GlobalSign 组织验证 CA - G2
- GlobalSign 根 CA
- Microsoft Authenticode(tm) 根颁发机构
- Microsoft Exchange Services CA 2015
- Microsoft 内部企业根
- Microsoft IT ITO SSL CA 1
- Microsoft IT SSL SHA1
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft 根颁发机构
- Microsoft 根证书颁发机构
- Microsoft 根证书颁发机构 2010
- Microsoft 根证书颁发机构 2011
- Microsoft 安全服务器 CA 2011
- Microsoft 服务合作伙伴根
- Microsoft 时间戳服务根
- Microsoft Windows 硬件兼容性
- MSIT CA Z2
- MSIT 企业 CA 1
- MSIT 企业 CA 3
- 根代理
- Symantec 类 3 EV SSL CA - G3
- Symantec 类 3 安全服务器 CA - G4
- 适用于 Microsoft 的 Symantec 企业移动根
- Thawte 时间戳 CA
- UTN-USERFirst-Object
- VeriSign 类 3 扩展验证 SSL CA
- VeriSign 类 3 扩展验证 SSL SGC CA
- VeriSign 类 3 公共主要证书颁发机构 - G5
- VeriSign 国际服务器 CA - 类 3
- VeriSign 时间戳服务根
- VeriSign 通用根证书颁发机构
- WMSvc-SHA2-DALEDGE1008

