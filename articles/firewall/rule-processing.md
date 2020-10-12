---
title: Azure 防火墙规则处理逻辑
description: Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087537"
---
# <a name="configure-azure-firewall-rules"></a>配置 Azure 防火墙规则
在 Azure 防火墙上可以配置 NAT 规则、网络规则和应用程序规则。 处理规则集合时，会根据规则类型按优先级顺序（由低编号到高编号，从 100 到 65,000）进行。 规则集合名称只能包含字母、数字、下划线、句点或连字符。 该名称必须以字母或数字开头，并且以字母、数字或下划线结尾。 名称最大长度为 80 个字符。

最好在最初以 100 为增量（100、200、300，依此类推）设置规则集合优先级编号，这样在需要时就还有空间，可以添加更多的规则集合。

> [!NOTE]
> 如果启用基于威胁情报的筛选，则那些规则具有最高优先级，始终会首先处理。 在处理任何已配置的规则之前，威胁情报筛选可能会拒绝流量。 有关详细信息，请参阅 [Azure 防火墙基于威胁情报的筛选](threat-intel.md)。

## <a name="outbound-connectivity"></a>出站连接

### <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则

如果配置了网络规则和应用程序规则，则会在应用程序规则之前先按优先级顺序应用网络规则。 规则将终止。 因此，如果在网络规则中找到了匹配项，则不会处理其他规则。  如果没有网络规则匹配项，并且，如果协议是 HTTP、HTTPS 或 MSSQL，则应用程序规则会按优先级顺序评估数据包。 如果仍未找到匹配项，则会根据[基础结构规则集合](infrastructure-fqdns.md)评估数据包。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

## <a name="inbound-connectivity"></a>入站连接

### <a name="nat-rules"></a>NAT 规则

可以通过配置目标网络地址转换 (DNAT) 来启用入站 Internet 连接，如[教程：使用 Azure 门户通过 Azure Firewall DNAT 筛选入站流量](tutorial-firewall-dnat.md)中所述。 NAT 规则会在网络规则之前按优先级应用。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。

应用程序规则不适用于入站连接。 因此，如果要筛选入站 HTTP/S 流量，应使用 Web 应用程序防火墙 (WAF) 。 有关详细信息，请参阅 [什么是 Azure Web 应用程序防火墙？](../web-application-firewall/overview.md)

## <a name="examples"></a>示例

下面的示例显示了组合使用其中一些规则时的结果。

### <a name="example-1"></a>示例 1

允许连接到 google.com，因为存在匹配的网络规则。

网络规则

- 操作：允许


|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|IP 地址|*|IP 地址|*|80,443

应用程序规则

- 操作：Deny

|name  |源类型  |源  |协议:端口|目标 FQDN|
|---------|---------|---------|---------|----------|----------|
|拒绝-google     |IP 地址|*|http:80,https:443|google.com

**结果**

允许连接到 google.com，因为数据包与 *允许 web* 网络规则匹配。 此时，规则处理停止。

### <a name="example-2"></a>示例 2

由于优先级较高的 Deny 网络规则集合阻止 SSH 流量，因此 SSH 流量被拒绝。

网络规则集合 1

- 姓名：Allow-collection
- 优先级：200
- 操作：允许

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP 地址|*|IP 地址|*|22

网络规则集合 2

- 姓名：Deny-collection
- 优先级：100
- 操作：Deny

|name  |协议  |源类型  |源  |目标类型  |目标地址  |目标端口|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP 地址|*|IP 地址|*|22

**结果**

由于优先级较高的网络规则集合阻止 SSH 连接，因此 SSH 连接被拒绝。 此时，规则处理停止。

## <a name="rule-changes"></a>规则更改

如果更改规则以拒绝以前允许的流量，则会删除任何相关的现有会话。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。