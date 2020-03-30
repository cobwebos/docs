---
title: Azure 防火墙规则处理逻辑
description: Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264773"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火墙规则处理逻辑
您可以在 Azure 防火墙上配置 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。 

> [!NOTE]
> 如果启用基于威胁智能的筛选，则这些规则是最高优先级，并且始终首先处理。 威胁智能筛选可能会在处理任何配置的规则之前拒绝流量。 有关详细信息，请参阅[Azure 防火墙威胁基于智能的筛选](threat-intel.md)。

## <a name="outbound"></a>出站

### <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则

如果配置网络规则和应用程序规则，则网络规则在应用程序规则之前按优先级顺序应用。 规则将终止。 因此，如果在网络规则中找到匹配项，则不会处理其他规则。  如果没有网络规则匹配，并且协议是 HTTP、HTTPS 或 MSSQL，则应用程序规则按优先级顺序计算数据包。 如果仍未找到匹配项，则根据[基础结构规则集合](infrastructure-fqdns.md)对数据包进行评估。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

## <a name="inbound"></a>入站

### <a name="nat-rules"></a>NAT 规则

可以通过配置目标网络地址转换 （DNAT） 来启用入站互联网连接，如教程中所述[：使用 Azure 门户使用 Azure 防火墙 DNAT 筛选入站流量](tutorial-firewall-dnat.md)。 NAT 规则在网络规则之前在优先级上应用。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。

应用程序规则不适用于入站连接。 因此，如果要筛选入站 HTTP/S 流量，则应使用 Web 应用程序防火墙 （WAF）。 有关详细信息，请参阅什么是[Azure Web 应用程序防火墙？](../web-application-firewall/overview.md)

## <a name="examples"></a>示例

以下示例显示了其中一些规则组合的结果。

### <a name="example-1"></a>示例 1

由于匹配的网络规则，允许连接到google.com。

**网络规则**

- 操作：允许


|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|允许网     |TCP|IP 地址|*|IP 地址|*|80,443

**应用程序规则**

- 操作：拒绝

|name  |源类型  |源  |协议:端口|目标 FQDN|
|---------|---------|---------|---------|----------|----------|
|拒绝-谷歌     |IP 地址|*|http：80，https：443|google.com

**结果**

允许连接到google.com，因为数据包与*允许 Web*网络规则匹配。 此时规则处理将停止。

### <a name="example-2"></a>示例 2

SSH 流量被拒绝，因为优先级较高的*拒绝*网络规则集合会阻止它。

**网络规则集合 1**

- 名称：允许收集
- 优先级：200
- 操作：允许

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|允许-SSH     |TCP|IP 地址|*|IP 地址|*|22

**网络规则集合 2**

- 名称：拒绝集合
- 优先级：100
- 操作：拒绝

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|拒绝-SSH     |TCP|IP 地址|*|IP 地址|*|22

**结果**

SSH 连接被拒绝，因为优先级较高的网络规则集合会阻止它。 此时规则处理将停止。

## <a name="rule-changes"></a>规则更改

如果更改规则以拒绝以前允许的流量，则删除任何相关的现有会话。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。