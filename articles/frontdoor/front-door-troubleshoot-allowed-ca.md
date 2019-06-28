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
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330752"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>获准对 Azure Front Door 服务启用自定义 HTTPS 的证书颁发机构

对于 Azure Front Door 服务自定义域，如果[使用自己的证书启用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)，必须使用获准的证书颁发机构 (CA) 来创建 SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
