---
title: Azure 防火墙管理器规则处理逻辑
description: 了解 Azure 防火墙规则处理逻辑
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512174"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火墙规则处理逻辑

Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。

## <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则

首先将应用网络规则，然后应用应用程序规则。 规则将终止。 因此，如果在网络规则中找到匹配项，则不会处理应用程序规则。  如果没有匹配的网络规则，并且如果数据包协议为 HTTP/HTTPS，则应用程序规则将评估该数据包。 如果仍未找到匹配项，则会根据基础结构规则集合评估数据包。 如果仍没有匹配项，则默认情况下会拒绝数据包。

## <a name="nat-rules"></a>NAT 规则

可以通过配置目标网络地址转换 (DNAT) 来启用入站连接，如[教程：使用 Azure 门户通过 Azure Firewall DNAT 筛选入站流量](../firewall/tutorial-firewall-dnat.md)中所述。 首先将应用 DNAT 规则。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。 对于这些连接，不会应用应用程序规则。

## <a name="inherited-rules"></a>继承规则

继承自父策略的网络规则集始终优先于定义为新策略的一部分的网络规则集合。 相同的逻辑也适用于应用程序规则集合。 但是，不管是否继承，网络规则集合始终在应用程序规则集合之前进行处理。

默认情况下，策略会继承其父策略威胁智能模式。 可以通过在 "策略设置" 页中将威胁情报模式设置为其他值来覆盖此项。 只能使用更严格的值替代行为。 例如，如果 "家长策略" 设置为 "*仅警报*"，则可以将此本地策略配置为 "*警报" 和 "拒绝*"，但无法将其关闭。

## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 防火墙管理器的详细信息](overview.md)