---
title: "配置流量管理器故障转移流量路由方法 | Microsoft 文档"
description: "本文将帮助你在流量管理器中配置故障转移流量路由方法"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 1a6b13a0-5f23-4624-be64-606156f0dc4b
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 849e277bcd059c270d681a55b954a5b86f4a5935


---
<!-- repub for nofollow -->

# <a name="configure-failover-routing-method"></a>配置故障转移路由方法
组织通常希望为服务提供可靠性。 一般通过在主服务发生故障时提供备用服务来实现这一目的。 一种常用的服务故障转移模式是提供一组相同的服务并向主服务发送流量，同时，维护包含一个或多个备份服务的已配置列表。 你可以按照下面的过程使用 Azure 云服务和网站配置此类型的备份。

请注意，无论网站模式如何，Azure 网站都已经针对数据中心（也称为区域）内的网站提供了“故障转移”流量路由方法功能。 你可以使用流量管理器为不同数据中心内的网站指定故障转移流量路由方法。

## <a name="to-configure-failover-traffic-routing-method"></a>配置故障转移流量路由方法：
1. 在 Azure 经典门户中，在左窗格中，单击流量管理器图标以打开“**流量管理器**”窗格。 如果尚未创建流量管理器配置文件，请参阅[管理流量管理器配置文件](traffic-manager-manage-profiles.md)来了解创建基本的流量管理器配置文件的步骤。
2. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
3. 在你的配置文件页面上，单击页面顶部的“**终结点**”并验证你要在配置中包括的云服务和网站（终结点）是否都存在。 有关添加或删除终结点的步骤，请参阅[在流量管理器中管理终结点](traffic-manager-endpoints.md)。
4. 在你的配置文件页面上，单击顶部的“**配置**”以打开配置页面。
5. 对于“**流量路由方法设置**”，验证流量路由方法是否是“**故障转移**”。 如果不是，请在下拉列表中单击“**故障转移**”。
6. 对于“**故障转移优先级列表**”，请调整你的终结点的故障转移顺序。 如果选择了“**故障转移**”流量路由方法，则所选终结点的顺序很重要。 主终结点位于顶部。 请使用向上和向下箭头根据需要更改顺序。 有关如何使用 Windows PowerShell 设置故障转移优先级的信息，请参阅 [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880)。
7. 验证是否已正确配置了“**监视设置**”。 监视可确保不会向处于脱机状态的终结点发送流量。 为了监视终结点，必须指定路径和文件名。 注意，正斜杠“/”是有效的相对路径条目，表示该文件位于根目录（默认值）中。 有关监视的详细信息，请参阅[流量管理器监视](traffic-manager-monitoring.md)。
8. 完成你的配置更改后，单击页面底部的“**保存**”。
9. 测试你的配置更改。 有关详细信息，请参阅[测试流量管理器设置](traffic-manager-testing-settings.md)。
10. 在流量管理器配置文件完成设置并正常工作后，在你的权威 DNS 服务器上编辑 DNS 记录以将你的公司域名指向流量管理器域名。 有关如何执行此操作的详细信息，请参阅[将公司 Internet 域指向流量管理器域](traffic-manager-point-internet-domain.md)。

## <a name="next-steps"></a>后续步骤
[将公司 Internet 域指向流量管理器域](traffic-manager-point-internet-domain.md)

[流量管理器路由方法](traffic-manager-routing-methods.md)

[配置轮循机制路由方法](traffic-manager-configure-round-robin-routing-method.md)

[配置性能路由方法](traffic-manager-configure-performance-routing-method.md)

[流量管理器降级状态疑难解答](traffic-manager-troubleshooting-degraded.md)

[流量管理器 - 禁用、启用或删除配置文件](disable-enable-or-delete-a-profile.md)

[流量管理器 - 禁用或启用终结点](disable-or-enable-an-endpoint.md)




<!--HONumber=Nov16_HO3-->


