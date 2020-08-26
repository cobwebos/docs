---
title: 网络规则中的 Azure 防火墙 FQDN 筛选（预览版）
description: 如何使用网络规则中的 Azure 防火墙 FQDN 筛选
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 1a35d9c48dd46d5d220699589f4ed758d21feca8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854284"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>在网络规则中使用 FQDN 筛选 (预览) 

> [!IMPORTANT]
> 网络规则中的 FQDN 筛选目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

完全限定的域名 (FQDN) 表示主机或 IP 地址 (es) 的域名。 你可以在基于 Azure 防火墙和防火墙策略中的 DNS 解析的网络规则中使用 Fqdn。 利用此功能，可以通过任何 TCP/UDP 协议 (（包括 NTP、SSH、RDP，等等) ）筛选出站流量。 必须允许 DNS 代理在网络规则中使用 Fqdn。 有关详细信息，请参阅 [Azure 防火墙 DNS 设置 (预览) ](dns-settings.md)。

> [!NOTE]
> 按照设计，FQDN 筛选不支持通配符。

## <a name="how-it-works"></a>工作原理

一旦你定义了你的组织需要 (的 DNS 服务器 Azure DNS 或你自己的自定义 DNS) ，Azure 防火墙就会基于所选的 DNS 服务器，将 FQDN 转换为 (es) 的 IP 地址。 这种翻译发生在应用程序和网络规则处理中。

在应用程序规则中使用域名与网络规则相比，它们之间的区别是什么？ 

- HTTP/S 和 MSSQL 应用程序规则中的 FQDN 筛选基于应用程序级别的透明代理和 SNI 标头。 因此，它可以在两个解析为同一 IP 地址的 Fqdn 之间进行识别。 对于网络规则中的 FQDN 筛选，情况并非如此。 如果可能，请始终使用应用程序规则。
- 在应用程序规则中，可以使用 HTTP/S 和 MSSQL 作为所选协议。 在 "网络规则" 中，可以将任何 TCP/UDP 协议与目标 Fqdn 结合使用。

## <a name="next-steps"></a>后续步骤

[Azure 防火墙 DNS 设置](dns-settings.md)
