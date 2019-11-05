---
title: Azure 防火墙规则处理逻辑
description: 了解 Azure 防火墙规则处理逻辑
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518198"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火墙规则处理逻辑

Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。

## <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则

首先将应用网络规则，然后应用应用程序规则。 规则将终止。 因此，如果在网络规则中找到匹配项，则不会处理应用程序规则。  如果没有网络规则匹配项，并且数据包协议是 HTTP/HTTPS，则不会按应用程序规则评估数据包。 如果仍未找到匹配项，则会根据基础结构规则集合评估数据包。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

## <a name="nat-rules"></a>NAT 规则

可以通过配置目标网络地址转换 (DNAT) 来启用入站连接，如[教程：使用 Azure 门户通过 Azure Firewall DNAT 筛选入站流量](../firewall/tutorial-firewall-dnat.md)中所述。 首先将应用 DNAT 规则。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。 对于这些连接，不会应用应用程序规则。

## <a name="inherited-rules"></a>继承规则

继承自父策略的网络规则集始终优先于定义为新策略的一部分的网络规则集合。 相同的逻辑也适用于应用程序规则集合。 但是，在应用程序规则集合之前始终处理网络规则集合，而不考虑继承。

默认情况下，策略会继承其父策略威胁智能模式。 可以通过在 "策略设置" 页中将威胁情报模式设置为其他值来覆盖此项。 只能用更严格的值进行重写。 例如，如果 "家长策略" 设置为 "*仅警报*"，则可以将此本地策略配置为 "*警报" 和 "拒绝*"，但无法将其关闭。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 防火墙管理器预览版](overview.md)