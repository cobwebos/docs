---
title: 检索 Azure CDN 的当前 POP IP 列表 |Microsoft Docs
description: 了解如何检索当前 POP 列表。
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
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: f677d6b8edfe60646c6368acce9d47b23a35237d
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146887"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 POP IP 列表

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 Verizon POP IP 列表

REST API 可用于检索 Verizon 入网点 (POP) 服务器的 IP 集。 这些 POP 服务器向源服务器发出请求，源服务器与 Verizon 配置文件（Verizon 提供的 Azure CDN 标准版或 Verizon 提供的 Azure CDN 高级版）上的 Azure 内容分发网络 (CDN) 终结点相关联。 请注意，此 IP 集与客户端在向 POP 发出请求时看到的 IP 不同。 

有关用于检索 POP 列表的 REST API 操作语法，请参阅[边缘节点 - 列表](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)。

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>检索 Azure CDN 的当前 Microsoft POP IP 列表

若要将应用程序锁定为仅接受来自 Microsoft Azure CDN 的流量，需要为后端设置 IP Acl。 你还可以限制从 Microsoft Azure CDN 发送的标头 "X-转发主机" 的已接受值集。 下面详细说明了这些步骤：

将后端的 IP Acl 配置为仅接受来自 Microsoft 后端 IP 地址空间和 Azure 的基础结构服务 Azure CDN 的流量。 

* 从 Microsoft 的 IPv4 后端 IP 空间 Azure CDN：147.243.0.0/16
* 从 Microsoft 的 IPv6 后端 IP 空间 Azure CDN：2a01：111：2050：：/44

可在[此处](https://www.microsoft.com/download/details.aspx?id=56519)找到 Microsoft 服务的 IP 范围和服务标记

筛选从 Microsoft Azure CDN 发送的传入标头 "X-转发主机" 的值。 标头唯一允许的值应该是 CDN 配置中定义的所有终结点主机。事实上，在您自己的特定来源，只是想要接受其流量的主机名。

## <a name="typical-use-case"></a>典型用例

出于安全方面的考虑，可以使用此 IP 列表强制仅从有效的 Verizon POP 向源服务器发出请求。 例如，如果有人发现了 CDN 终结点源服务器的主机名或 IP 地址，就可以直接向源服务器发出请求，从而绕过 Azure CDN 提供的缩放和安全功能。 通过将返回列表中的 IP 设置为源服务器上仅允许的 IP，可以防止这种情况出现。 为了确保 POP 列表为最新，至少每天检索一次。 

## <a name="next-steps"></a>后续步骤

若要了解 REST API，请参阅 [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)。
