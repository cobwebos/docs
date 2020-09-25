---
title: 允许 CA 在 Azure 前门上启用自定义 HTTPS
description: 如果要使用自己的证书在 Azure 前门0custom 域上启用 HTTPS，则必须使用允许的证书颁发机构 (CA) 来创建它。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269789"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>允许用于在 Azure 前门上启用自定义 HTTPS 的证书颁发机构

对于 Azure 前门自定义域，当你 [使用自己的证书启用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)时，必须使用允许的证书颁发机构 (CA) 来创建 TLS/SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
