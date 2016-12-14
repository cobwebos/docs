---
title: "配置性能流量路由方法 | Microsoft 文档"
description: "本文将帮助你在流量管理器中配置性能流量路由方法"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 6dd23b8e-0ed5-4ea4-b5ae-018f42e72688
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51a3f970059f4b83240cb61411dbf612209d9293


---
<!-- repub for nofollow -->

# <a name="configure-performance-traffic-routing-method"></a>配置性能流量路由方法
若要对分布在全球不同数据中心（也称为区域）的云服务和网站（终结点）进行流量路由，可以将传入的流量从发出请求的客户端定向到延迟最低的终结点。 通常，延迟最低的数据中心对应于地理距离最短的终结点。 使用“性能”流量路由方法可以基于最低延迟进行分发，但无法考虑网络配置或负载中的实时变化。 有关 Azure 流量管理器提供的各种流量路由方法的详细信息，请参阅[关于流量管理器流量路由方法](traffic-manager-routing-methods.md)。

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>根据最低延迟在一组终结点之间路由流量：
1. 在 Azure 经典门户中，在左窗格中，单击流量管理器图标以打开“**流量管理器**”窗格。 如果尚未创建流量管理器配置文件，请参阅[管理流量管理器配置文件](traffic-manager-manage-profiles.md)，了解创建基本的流量管理器配置文件的步骤。
2. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
3. 在你的配置文件页面上，单击页面顶部的“**终结点**”，并验证你要在配置中包含的服务终结点是否都存在。 有关在配置文件中添加或删除终结点的步骤，请参阅[在流量管理器中管理终结点](traffic-manager-endpoints.md)。
4. 在你的配置文件页面上，单击顶部的“**配置**”以打开配置页面。
5. 对于“**流量路由方法设置**”，请检查流量路由方法是否为“**性能*”。如果不是，请在下拉列表中单击“**性能**”。
6. 验证是否已正确配置了“**监视设置**”。 监视可确保不会向处于脱机状态的终结点发送流量。 为了监视终结点，必须指定路径和文件名。 注意，正斜杠“/”是有效的相对路径条目，表示该文件位于根目录（默认值）中。 有关监视的详细信息，请参阅[关于流量管理器监视](traffic-manager-monitoring.md)。
7. 完成你的配置更改后，单击页面底部的“**保存**”。
8. 测试你的配置更改。 有关详细信息，请参阅[测试流量管理器设置](traffic-manager-testing-settings.md)。
9. 在流量管理器配置文件完成设置并正常工作后，在你的权威 DNS 服务器上编辑 DNS 记录以将你的公司域名指向流量管理器域名。 有关如何执行此操作的详细信息，请参阅[将公司 Internet 域指向流量管理器域](traffic-manager-point-internet-domain.md)。

## <a name="next-steps"></a>后续步骤
[将公司 Internet 域指向流量管理器域](traffic-manager-point-internet-domain.md)

[流量管理器路由方法](traffic-manager-routing-methods.md)

[配置故障转移路由方法](traffic-manager-configure-failover-routing-method.md)

[配置轮循机制路由方法](traffic-manager-configure-round-robin-routing-method.md)

[流量管理器降级状态疑难解答](traffic-manager-troubleshooting-degraded.md)

[流量管理器 - 禁用、启用或删除配置文件](disable-enable-or-delete-a-profile.md)

[流量管理器 - 禁用或启用终结点](disable-or-enable-an-endpoint.md)




<!--HONumber=Nov16_HO3-->


