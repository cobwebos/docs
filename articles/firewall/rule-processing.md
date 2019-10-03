---
title: Azure 防火墙规则处理逻辑
description: 了解 Azure 防火墙规则处理逻辑
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681588"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火墙规则处理逻辑
Azure 防火墙具有 NAT 规则、网络规则和应用程序规则。 规则是根据规则类型进行处理的。


## <a name="network-rules-and-applications-rules"></a>网络规则和应用程序规则 
首先将应用网络规则，然后应用应用程序规则。 规则将终止。 因此，如果在网络规则中找到匹配项，则不会处理应用程序规则。  如果没有网络规则匹配项，并且数据包协议是 HTTP/HTTPS，则会按应用程序规则评估数据包。 如果仍未找到匹配项，则会根据基础结构规则集合评估数据包。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

## <a name="nat-rules"></a>NAT 规则
可以通过配置目标网络地址转换 (DNAT) 中所述启用入站的连接[教程：与使用 Azure 门户的 Azure 防火墙 DNAT 筛选入站的流量](tutorial-firewall-dnat.md)。 首先将应用 DNAT 规则。 如果找到匹配项，则会添加一个隐式的对应网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。 对于这些连接，不会应用应用程序规则。


## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。