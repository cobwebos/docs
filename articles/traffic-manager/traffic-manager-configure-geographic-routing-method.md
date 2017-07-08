---
title: "使用 Azure 流量管理器配置地理流量路由方法 | Microsoft Docs"
description: "本文介绍如何使用 Azure 流量管理器配置地理流量路由方法"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 8bbcb87f2ce8aefddcb174eb80a7a6f7fb16b752
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017

---

# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>使用流量管理器配置地理流量路由方法

使用地理流量路由方法，可基于请求源自的地理位置将流量定向到特定终结点。 本教程说明如何使用此路由方法创建流量管理器配置文件，并配置终结点以从特定地理区域接收流量。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1. 在浏览器中，登录 [Azure 门户](http://portal.azure.com)。 如果还没有帐户，可以注册[免费一个月试用版](https://azure.microsoft.com/free/)。
2. 在“中心”菜单上，单击“新建” > “网络” > “全部查看”，然后单击“流量管理器配置文件”，打开“创建流量管理器配置文件”边栏选项卡。
3. 在“创建流量管理器配置文件”边栏选项卡上：
    1. 提供配置文件的名称。 此名称必须在 trafficmanager.net 区域中唯一，并将生成 DNS 名称（<profilename>,trafficmanager.net），该名称将用于访问流量管理器配置文件。
    2. 选择“地理”路由方法。
    3. 选择要创建此配置文件的订阅。
    4. 使用现有资源组，或创建新的资源组，以在其下放置此配置文件。 如果选择创建新的资源组，请使用“资源组位置”下拉列表来指定资源组位置。 此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。
    5. 单击“创建”后，将创建并全局部署流量管理器配置文件。

![创建流量管理器配置文件](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>添加终结点

1. 在门户的搜索栏中搜索你刚创建的流量管理器配置文件名称，然后在显示结果后单击该结果。
2. 导航到“流量管理器”边栏选项卡中的“设置” -> “终结点”。
3. 单击“添加”以显示“添加终结点”边栏选项卡。
3. 在“终结点”边栏选项卡中，单击“添加”，然后在显示的“添加终结点”边栏选项卡中，按如下所示完成输入：
4. 选择**类型**，具体取决于要添加的终结点的类型。 对于生产中使用的地理路由配置文件，我们强烈建议使用包含有多个终结点的子配置文件的嵌套终结点类型。 有关更多详细信息，请参阅[有关地理流量路由方法的常见问题](traffic-manager-FAQs.md)。
5. 提供要用于识别此终结点的**名称**。
6. 此边栏选项卡中的某些字段取决于要添加的终结点的类型：
    1. 如果要添加 Azure 终结点，请根据要将流量定向到的资源选择**目标资源类型**和**目标**
    2. 如果要添加**外部**终结点，请为终结点提供**完全限定域名(FQDN)**。
    3. 如果要添加**嵌套终结点**，请选择与要使用的子配置文件对应的**目标资源**，并指定**最小子终结点计数**。
7. 在“异地映射”部分中，使用下拉列表添加要从中将流量发送到此终结点的区域。 必须至少添加一个区域，并且可以映射多个区域。
8. 对于要在此配置文件下添加的所有终结点重复此操作

![添加流量管理器终结点](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>使用流量管理器配置文件
1.  在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称，然后在显示的结果中单击该流量管理器配置文件。
2. 在“流量管理器配置文件”边栏选项卡中，单击“概述”。
3. “流量管理器配置文件”边栏选项卡会显示新建的流量管理器配置文件的 DNS 名称。 任何客户端（例如，通过使用 Web 浏览器导航到客户端）均可使用此名称来路由到由路由类型确定的相应终结点。  对于地理路由，流量管理器查看传入请求的源 IP，并确定该请求所源自的区域。 如果该区域映射到某个终结点，则流量将路由到该终结点。 如果该区域未映射到终结点，则流量管理器将返回“无数据”查询响应。

## <a name="next-steps"></a>后续步骤

- 深入了解[地理流量路由方法](traffic-manager-routing-methods.md#a-name--geographicageographic-traffic-routing-method)。
- 了解如何[测试流量管理器设置](traffic-manager-testing-settings.md)。

