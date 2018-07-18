---
title: 检索 Azure CDN 的当前 Verizon POP 列表 | Microsoft Docs
description: 了解如何使用 REST API 检索当前的 Verizon POP 列表。
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
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754166"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>检索 Azure CDN 的当前 Verizon POP 列表

REST API 可用于检索 Verizon 入网点 (POP) 服务器的 IP 集。 这些 POP 服务器向源服务器发出请求，源服务器与 Verizon 配置文件（Verizon 提供的 Azure CDN 标准版或 Verizon 提供的 Azure CDN 高级版）上的 Azure 内容分发网络 (CDN) 终结点相关联。 请注意，此 IP 集与客户端在向 POP 发出请求时看到的 IP 不同。 

有关用于检索 POP 列表的 REST API 操作语法，请参阅[边缘节点 - 列表](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)。

## <a name="typical-use-case"></a>典型用例

出于安全方面的考虑，可以使用此 IP 列表强制仅从有效的 Verizon POP 向源服务器发出请求。 例如，如果有人发现了 CDN 终结点源服务器的主机名或 IP 地址，就可以直接向源服务器发出请求，从而绕过 Azure CDN 提供的缩放和安全功能。 通过将返回列表中的 IP 设置为源服务器上仅允许的 IP，可以防止这种情况出现。 为了确保 POP 列表为最新，至少每天检索一次。 

## <a name="next-steps"></a>后续步骤

若要了解 REST API，请参阅 [Azure CDN REST API](https://docs.microsoft.com/en-us/rest/api/cdn/)。