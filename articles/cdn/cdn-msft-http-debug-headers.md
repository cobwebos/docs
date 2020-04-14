---
title: 调试来自微软的 Azure CDN 的 HTTP 标头 |微软文档
description: 调试缓存请求标头提供有关应用于请求资产的缓存策略的其他信息。 这些标头特定于来自 Microsoft 的 Azure CDN。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260405"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>调试来自微软的 Azure CDN 的 HTTP 标头
调试响应标头`X-Cache`提供了内容从哪层提供的 CDN 堆栈的详细信息。 此标头特定于来自 Microsoft 的 Azure CDN。

### <a name="response-header-format"></a>响应标头格式

标头 | 说明
-------|------------
X-Cache： TCP_HIT | 当从 CDN 边缘缓存提供内容时，将返回此标头。 
X-Cache： TCP_REMOTE_HIT | 当从 CDN 区域缓存（源屏蔽层）提供内容时，将返回此标头
X-Cache： TCP_MISS | 当缓存未命中时，将返回此标头，并且内容从"原点"提供。 


