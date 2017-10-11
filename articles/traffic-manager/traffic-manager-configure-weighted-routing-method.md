---
title: "使用 Azure 流量管理器配置加权轮循机制流量路由方法 | Microsoft Docs"
description: "本文介绍如何在流量管理器中使用轮循机制方法加载均衡流量"
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
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>在流量管理器中配置加权流量路由方法

一种常见的流量路由方法模式是提供一组相同的终结点（包括云服务和网站），并以循环方式向每个终结点发送流量。 以下步骤概述如何配置这种类型的流量路由方法。

> [!NOTE]
> Azure 网站已针对数据中心（也称为区域）内的网站提供了轮询机制负载均衡功能。 可以使用流量管理器为不同数据中心内的网站指定轮询机制流量路由方法。

## <a name="to-configure-the-weighted-traffic-routing-method"></a>配置加权流量路由方法

1. 在浏览器中，登录 [Azure 门户](http://portal.azure.com)。 如果还没有帐户，可以注册[免费一个月试用版](https://azure.microsoft.com/free/)。 
2. 在门户的搜索栏中，搜索“流量管理器配置文件”，并单击要为其配置路由方法的配置文件名称。
3. 在“流量管理器配置文件”边栏选项卡中，检查要包含在配置中的云服务和网站是否都存在。
4. 在“设置”部分，单击“配置”，并在“配置”边栏选项卡中完成如下操作：
    1. 对于“流量路由方法设置”，验证流量路由方法是否是“加权”。 如果不是，请在下拉列表中单击“加权”。
    2. 为此配置文件中的所有终结点设置相同的“终结点监视器设置”，如下所示：
        1. 选择相应的“协议”，并指定“端口”号。 
        2. 对于“路径”，请键入正斜杠 */*。 若要监视终结点，必须指定路径和文件名。 正斜杠“/”是有效的相对路径条目，表示文件位于根目录（默认位置）中。
        3. 在页面顶部，单击“保存”。
5. 按如下方式测试配置更改：
    1.  在门户的搜索栏中，搜索流量管理器配置文件名称，并在显示的结果中单击该流量管理器配置文件。
    2.  在“流量管理器配置文件”边栏选项卡中，单击“概述”。
    3.  “流量管理器配置文件”边栏选项卡会显示新建的流量管理器配置文件的 DNS 名称。 任意客户端（例如通过 Web 浏览器导航到此名称）均可使用此名称路由到根据路由类型确定的相应终结点。 在这种情况下，所有请求都以轮循机制的方式路由每个终结点。
6. 流量管理器配置文件正常工作后，请在权威 DNS 服务器上编辑 DNS 记录，将公司域名指向流量管理器域名。

![使用流量管理器配置加权流量路由方法][1]

## <a name="next-steps"></a>后续步骤

- 了解[优先级流量路由方法](traffic-manager-configure-priority-routing-method.md)。
- 了解[性能流量路由方法](traffic-manager-configure-performance-routing-method.md)。
- 了解[地理路由方法](traffic-manager-configure-geographic-routing-method.md)。
- 了解如何[测试流量管理器设置](traffic-manager-testing-settings.md)。

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
