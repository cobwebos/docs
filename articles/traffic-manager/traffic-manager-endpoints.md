---
title: "在 Azure 流量管理器中管理终结点 | Microsoft Docs"
description: "本文将帮助你从 Azure 流量管理器中添加、删除、启用和禁用终结点。"
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: tysonn
ms.assetid: 038270d1-28ba-4078-9c5d-37fc5d683be6
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cbdd48397319cb840b05570ba90093ff8f6887b0

---

# <a name="add-disable-enable-or-delete-endpoints"></a>添加、禁用、启用或删除终结点

无论网站模式如何，Azure App Service 中的 Web 应用功能都已针对数据中心内的网站提供了故障转移和轮循机制流量路由功能。 你可以使用 Azure 流量管理器为不同数据中心内的网站和云服务指定故障转移和轮询机制流量路由。 提供该功能所需的第一个步骤是将云服务或网站终结点添加到流量管理器中。

> [!NOTE]
> 无法使用 Azure 经典门户将外部位置或流量管理器配置文件添加为终结点。 必须使用 REST API [创建定义](http://go.microsoft.com/fwlink/p/?LinkId=400772)或 Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774)。

你还可以禁用属于流量管理器配置文件的一部分的个体终结点。 终结点包括云服务和网站。 禁用某个终结点会将其保留为配置文件的一部分，但是配置文件的行为就如同其中不包括该终结点一样。 此操作对于临时删除处于维护模式或正在重新部署的终结点非常有用。 终结点再次运行后，可以启用它。

> [!NOTE]
> 禁用某个终结点对其在 Azure 中的部署状态没有任何影响。 正常的终结点将保持运行并能够接收流量，即使在流量管理器中已将其禁用也是如此。 此外，在一个配置文件中禁用某个终结点不会影响它在其他配置文件中的状态。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>添加云服务或网站终结点

1. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的终结点设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
2. 在页面顶部，单击“终结点”来查看已经属于你的配置的一部分的终结点。
3. 在页面底部，单击“添加”来访问“添加服务终结点”页。 默认情况下，该页面会在“服务终结点”下面列出云服务。
4. 对于云服务，请在列表中选择云服务以将其启用为此配置文件的终结点。 清除云服务名称会将其从终结点列表中删除。
5. 对于网站，请单击“服务类型”下拉列表，然后选择“Web 应用”。
6. 在列表中选择网站以将其添加为此配置文件的终结点。 清除网站名称会将其从终结点列表中删除。 注意，对于每个 Azure 数据中心（也称为区域），只能选择一个网站。 如果你在托管着多个网站的数据中心内选择网站，当你选择了第一个网站时，同一数据中心内的其他网站将变得不可供选择。 另请注意，只会列出标准网站。
7. 在为此配置文件选择终结点后，单击右下角的复选标记来保存你的更改。

> [!NOTE]
> 如果使用的是“故障转移”流量路由方法，添加或删除终结点后，请务必在“配置”页面上调整故障转移优先级列表以反映配置所需的故障转移顺序。 有关详细信息，请参阅[配置故障转移流量路由](traffic-manager-configure-failover-routing-method.md)。

## <a name="to-disable-an-endpoint"></a>禁用终结点

1. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的终结点设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
2. 在页面顶部，单击“终结点”来查看配置中包括的终结点。
3. 单击要禁用的终结点，然后单击页面底部的“禁用”。
4. 流量将根据为流量管理器域名配置的 DNS 生存时间 (TTL) 停止流向该终结点。 你可以从流量管理器配置文件的“配置”页面更改 TTL。

## <a name="to-enable-an-endpoint"></a>启用终结点

1. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的终结点设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
2. 在页面顶部，单击“终结点”来查看配置中包括的终结点。
3. 单击要启用的终结点，然后单击页面底部的“启用”。
4. 流量将重新开始流向配置文件所指定的服务。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>删除云服务或网站终结点

1. 在 Azure 经典门户的“流量管理器”窗格中，找到包含要修改的终结点设置的流量管理器配置文件，然后单击配置文件名称右侧的箭头。 这将打开配置文件的设置页面。
2. 在页面顶部，单击“终结点”来查看已经属于你的配置的一部分的终结点。
3. 在“终结点”页面上，单击你要从配置文件中删除的终结点的名称。
4. 在页面的底部，单击“删除”。

> [!NOTE]
> 无法使用 Azure 经典门户删除作为终结点的外部位置或流量管理器配置文件。 必须使用 Windows PowerShell。 有关详细信息，请参阅 [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx)。

## <a name="next-steps"></a>后续步骤

* [配置故障转移路由方法](traffic-manager-configure-failover-routing-method.md)
* [配置轮循机制路由方法](traffic-manager-configure-round-robin-routing-method.md)
* [配置性能路由方法](traffic-manager-configure-performance-routing-method.md)
* [流量管理器降级状态疑难解答](traffic-manager-troubleshooting-degraded.md)
* [流量管理器上的操作（REST API 参考）](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


