---
title: 调试 Microsoft Azure CDN 的 HTTP 标头 |Microsoft Docs
description: 调试缓存请求标头提供有关应用到所请求资产的缓存策略的其他信息。 这些标头特定于 Microsoft Azure CDN。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: be6655c2c5e6b2acca82b4a1b20a3cdf84e0251b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707521"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>调试 Microsoft Azure CDN 的 HTTP 标头
调试响应标头`X-Cache`提供有关内容所提供的 CDN 堆栈层的详细信息。 此标头特定于 Microsoft Azure CDN。

### <a name="response-header-format"></a>响应标头格式

Header | 描述
-------|------------
X 缓存:TCP_HIT | 从 CDN 边缘缓存提供内容时, 将返回此标头。 
X 缓存:TCP_REMOTE_HIT | 当通过 CDN 区域缓存 (源盾牌层) 提供内容时, 将返回此标头
X 缓存:TCP_MISS | 如果缓存未命中, 并从原始位置提供内容, 则会返回此标头。 


