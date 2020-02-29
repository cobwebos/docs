---
title: 允许的 CA 启用 Azure CDN 上的自定义 HTTPS
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919968"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>获准对 Azure CDN 启用自定义 HTTPS 的证书颁发机构

使用自己的 Azure 内容分发网络（CDN）自定义域的[证书启用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)时，必须满足特定的证书要求。 Microsoft 配置文件中的**Azure CDN 标准**要求以下列表中的一个批准的证书颁发机构（CA）的证书。 如果来自未经批准的 CA 的证书或使用了自签名证书，则将拒绝该请求。 **来自 Verizon 的标准 Azure CDN 标准** **Azure CDN 和 Verizon**配置文件中的高级配置文件均接受来自任何有效 CA 的任何有效证书。

> [!NOTE]
> 使用自己的证书启用自定义域 HTTPS 功能的*选项不可用于*Akamai 配置文件中的**Azure CDN Standard** 。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

