---
title: 网络规则中的 Azure 防火墙 FQDN 筛选（预览）
description: 如何在网络规则中使用 Azure 防火墙 FQDN 筛选
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7a30238250c9fcb86f1cc01226d44ab385c61843
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086619"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>在网络规则中使用 FQDN 筛选（预览）

> [!IMPORTANT]
> 网络规则中的 FQDN 筛选目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

完全限定的域名（FQDN）表示主机的域名。 域名与单个或多个 IP 地址相关联。 可以在应用程序规则中允许或阻止 fqdn 和 FQDN 标记。 使用自定义 DNS 和 DNS 代理设置，还可以在网络规则中使用 FQDN 筛选。

## <a name="how-it-works"></a>工作原理

Azure 防火墙使用其 DNS 设置将 FQDN 转换为 IP 地址，并根据 Azure DNS 或自定义 DNS 配置执行规则处理。

若要在网络规则中使用 Fqdn，应启用 DNS 代理。 如果未启用 DNS 代理，可靠的规则处理有风险。 启用后，DNS 流量定向到 Azure 防火墙，你可以在其中配置自定义 DNS 服务器。 然后，防火墙和客户端使用相同的配置 DNS 服务器。 如果未启用 DNS 代理，Azure 防火墙可能会产生不同的响应，因为客户端和防火墙可能会使用不同的服务器进行名称解析。 如果客户端和防火墙收到不同的 DNS 响应，则可能导致网络规则中的 FQDN 筛选出现故障或不一致。

可以通过在规则集合中选择 "**保存**" 之前确认风险来选择覆盖此要求。

## <a name="next-steps"></a>后续步骤

[Azure 防火墙 DNS 设置](dns-settings.md)
