---
title: Azure 防火墙规则处理逻辑
description: Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 69c0c13c7027707cdadb2f1f1de9cc1655c9c625
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621888"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火墙规则处理逻辑
可以在 Azure 防火墙上配置 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。 

> [!NOTE]
> 如果启用基于威胁智能的筛选，则这些规则是最高优先级，并且始终首先处理。 在处理任何配置的规则之前，威胁智能筛选可能会拒绝流量。 有关详细信息，请参阅[基于 Azure 防火墙威胁智能的筛选](threat-intel.md)。

## <a name="outbound"></a>出站

### <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则

如果配置网络规则和应用程序规则，则会在应用程序规则之前按优先级顺序应用网络规则。 规则将终止。 因此，如果在网络规则中找到匹配项，则不会处理其他规则。  如果没有匹配的网络规则，且协议为 HTTP、HTTPS 或 MSSQL，则应用程序规则将按优先级顺序对数据包进行评估。 如果仍找不到匹配项，则将根据[基础结构规则集合](infrastructure-fqdns.md)来计算数据包。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

## <a name="inbound"></a>入站

### <a name="nat-rules"></a>NAT 规则

可以通过配置目标网络地址转换（DNAT）来启用入站 Internet 连接，如[使用 Azure 门户使用 Azure 防火墙 DNAT 筛选入站流量](tutorial-firewall-dnat.md)中所述。 NAT 规则在网络规则之前按优先级应用。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。

应用程序规则不应用于入站连接。 因此，如果要筛选入站 HTTP/S 流量，应使用 Web 应用程序防火墙（WAF）。 有关详细信息，请参阅[什么是 Azure Web 应用程序防火墙？](../web-application-firewall/overview.md)

## <a name="examples"></a>示例

下面的示例显示了其中一些规则组合的结果。

### <a name="example-1"></a>示例 1

允许连接到 google.com，因为存在匹配的网络规则。

**网络规则**

- 操作：允许


|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|允许-web     |TCP|IP 地址|*|IP 地址|*|80,443

**应用程序规则**

- 操作： Deny

|name  |源类型  |源  |协议：端口|目标 Fqdn|
|---------|---------|---------|---------|----------|----------|
|拒绝-google     |IP 地址|*|http：80，https：443|google.com

**结果**

允许连接到 google.com，因为数据包与*允许 web*网络规则匹配。 规则处理此时停止。

### <a name="example-2"></a>示例 2

由于优先级较高的*拒绝*网络规则集合阻止 SSH 流量，因此拒绝了 SSH 流量。

**网络规则集合1**

- 名称：允许-集合
- 优先级：200
- 操作：允许

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|允许-SSH     |TCP|IP 地址|*|IP 地址|*|22

**网络规则集合2**

- 名称：拒绝-集合
- 优先级：100
- 操作： Deny

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP 地址|*|IP 地址|*|22

**结果**

SSH 连接被拒绝，因为较高优先级的网络规则集合阻止了 SSH 连接。 规则处理此时停止。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。