---
title: 允许 CA 在 Azure CDN 上启用自定义 HTTPS
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259963"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构

使用[自己的](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)证书访问 Azure 内容传递网络 （CDN） 自定义域时，必须满足特定的证书要求。 Microsoft 配置文件**中的 Azure CDN 标准**要求以下列表中已批准的证书颁发机构 （CA） 之一的证书。 如果来自未经批准的 CA 的证书或使用自签名证书，则请求将被拒绝。 **来自 Verizon 的 Azure CDN 标准版**和来自 Verizon 配置文件**的 Azure CDN 高级版**接受来自任何有效 CA 的任何有效证书。

> [!NOTE]
> 使用您自己的证书启用自定义域 HTTPS 功能的选项*不适用于*Akamai 配置文件中的**Azure CDN 标准**版。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

