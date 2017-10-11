---
title: "配置使用 Azure 流量管理器的优先级流量路由方法 |Microsoft 文档"
description: "此文章介绍了如何配置流量管理器中的优先级的流量路由方法"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>配置流量管理器中的优先级流量路由方法

无论网站模式中，Azure 网站已经提供了针对数据中心 （也称为区域） 内的网站的故障转移功能。 流量管理器提供不同数据中心内的网站的故障转移。

服务故障转移的常见模式是向主服务发送流量，并提供一组相同的备份服务进行故障转移。 以下步骤介绍如何使用 Azure 云服务和网站中配置此按优先级排列的故障转移：

## <a name="to-configure-the-priority-traffic-routing-method"></a>若要配置优先级的流量路由方法

1. 从浏览器中，登录到[Azure 门户](http://portal.azure.com)。 如果你还没有帐户，你可以注册[免费试用一个月](https://azure.microsoft.com/free/)。 
2. 在门户的搜索栏中，搜索**Traffic Manager 配置文件**，然后单击你想要配置的路由方法的配置文件名称。
3. 在**Traffic Manager 配置文件**边栏选项卡，验证是否存在的云服务和你想要包括在配置的网站。
4. 在**设置**部分中，单击**配置**，然后在**配置**边栏选项卡，完成，如下所示：
    1. 有关**流量路由方法设置**，验证流量路由方法是否**优先级**。 如果不存在，请单击**优先级**从下拉列表。
    2. 设置**终结点监视设置**相同的如下所示的此配置文件内所有每个终结点：
        1. 选择相应**协议**，并指定**端口**数。 
        2. 有关**路径**键入正斜杠 */* 。 若要监视终结点，必须指定路径和文件名。 一个正斜杠"/"是有效的相对路径条目，表示该文件位于根目录 （默认值）。
        3. 在页面顶部，单击**保存**。
5. 在**设置**部分中，单击**终结点**。
6. 在**终结点**边栏选项卡，查看你的终结点的优先级顺序。 当选择**优先级**流量路由方法，所选终结点非常重要的顺序。 验证终结点的优先级顺序。  主终结点位于顶部。 请仔细检查上显示的顺序。 所有请求将路由到第一个终结点，如果流量管理器检测到它处于不正常状态，流量故障自动转移到下一个终结点。 
7. 若要更改的终结点优先顺序，请单击的终结点，然后在**终结点**边栏选项卡中显示，请单击**编辑**和更改**优先级**值根据需要。 
8. 单击**保存**若要保存更改的终结点设置。
9. 完成你的配置更改后，单击**保存**位于页的底部。
10. 在你的配置中，如下所示测试所做的更改：
    1.  在门户的搜索栏中，搜索流量管理器配置文件名称，然后单击结果中的流量管理器配置文件的显示。
    2.  在**流量管理器**配置文件边栏选项卡，单击**概述**。
    3.  **Traffic Manager 配置文件**边栏选项卡显示你新创建的 Traffic Manager 配置文件的 DNS 名称。 这可由任何客户端 （例如，通过导航到它使用 web 浏览器） 来获取路由到正确终结点作为由路由类型。 在这种情况下的所有请求路由到第一个终结点和如果流量管理器检测到它处于不正常状态，流量故障自动转移到下一个终结点。
11. 一旦 Traffic Manager 配置文件正常，编辑权威 DNS 服务器以将公司域名指向流量管理器域名上的 DNS 记录。

![配置使用流量管理器的优先级流量路由方法][1]

## <a name="next-steps"></a>后续步骤


- 了解有关[加权流量路由方法](traffic-manager-configure-weighted-routing-method.md)。
- 了解有关[性能路由方法](traffic-manager-configure-performance-routing-method.md)。
- 了解有关[地理路由方法](traffic-manager-configure-geographic-routing-method.md)。
- 了解如何[测试流量管理器设置](traffic-manager-testing-settings.md)。

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png