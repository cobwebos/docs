---
title: "管理 Azure 流量管理器配置文件 |Microsoft 文档"
description: "本文可帮助你创建、 禁用、 启用和删除 Azure 流量管理器配置文件。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: a5164282264124835692bc72a4ab61891aa7af9d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-an-azure-traffic-manager-profile"></a>管理 Azure 流量管理器配置文件

Traffic Manager 配置文件使用流量路由方法控制分发给你的云服务或网站终结点的流量。 此文章介绍了如何创建和管理这些配置文件。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

你可以通过 Azure 门户创建 Traffic Manager 配置文件。 在创建后你的配置文件，你可以对在 Azure 门户中配置终结点、 监视和其他设置。 流量管理器支持每个配置文件的最多 200 终结点。 但是，大多数使用方案需要只有几个终结点。

### <a name="to-create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1. 从浏览器中，登录到[Azure 门户](http://portal.azure.com)。 如果你还没有帐户，你可以注册[免费试用一个月](https://azure.microsoft.com/free/)。 
2. 上**中心**菜单上，单击**新建** > **网络** > **查看所有**，单击**流量管理器**配置文件以打开**创建 Traffic Manager 配置文件**边栏选项卡，然后单击**创建**。
3. 上**创建 Traffic Manager 配置文件**边栏选项卡，完成，如下所示：
    1. 在**名称**，提供你的配置文件的名称。 此名称必须是唯一 trafficmanager.net 区域内，并导致的 DNS 名称<name>，trafficmanager.net，用于访问你的流量管理器配置文件。
    2. 在**路由方法**，选择**优先级**路由方法。
    3. 在**订阅**，选择你想要创建在此配置文件的订阅
    4. 在**资源组**，创建新的资源组，将在此配置文件。
    5. 在**资源组位置**，选择资源组的位置。 此设置引用的资源组、 位置以及不会影响对将全局部署的流量管理器配置文件。
    6. 单击“创建”。
    7. Traffic Manager 配置文件的全局部署完成后，它被列为各自资源组中的资源之一。

## <a name="disable-enable-or-delete-a-profile"></a>禁用、 启用或删除配置文件

你可以禁用现有配置文件，以便该流量管理器不是指用户请求配置的终结点。 当禁用流量管理器配置文件时，配置文件和配置文件中包含的信息保持不变，并且可在 Traffic Manager 界面中进行编辑。  当重新启用该配置文件时，将恢复引用。 当你在 Azure 门户中创建 Traffic Manager 配置文件时，它会自动启用。 如果你决定不再需要一个配置文件，你可以将其删除。

### <a name="to-disable-a-profile"></a>禁用配置文件

1. 如果使用自定义域名，更改你的 Internet DNS 服务器上的 CNAME 记录，以便它不再指向 Traffic Manager 配置文件。
2. 流量将停止定向到通过 Traffic Manager 配置文件设置的终结点。
3. 从浏览器中，登录到[Azure 门户](http://portal.azure.com)。
2. 在门户的搜索栏中，搜索**Traffic Manager 配置文件**你想要修改，，然后单击结果中的流量管理器配置文件的名称的显示。
3. 在**Traffic Manager 配置文件**边栏选项卡，单击**概述**，在概述边栏选项卡中，单击**禁用**，然后确认若要禁用流量管理器配置文件。

### <a name="to-enable-a-profile"></a>若要启用配置文件

1. 从浏览器中，登录到[Azure 门户](http://portal.azure.com)。
2. 在门户的搜索栏中，搜索**Traffic Manager 配置文件**你想要修改，，然后单击结果中的流量管理器配置文件的名称的显示。
3. 在**Traffic Manager 配置文件**边栏选项卡，单击**概述**，然后在概述边栏选项卡单击**启用**。
5. 如果使用自定义域名，请在你的 Internet DNS 服务器指向 Traffic Manager 配置文件的域名上创建的 CNAME 资源记录。
6. 流量将再次定向到终结点。

### <a name="to-delete-a-profile"></a>若要删除配置文件

1. 确保你的 Internet DNS 服务器上的 DNS 资源记录不再使用指向 Traffic Manager 配置文件的域名的 CNAME 资源记录。
2. 在门户的搜索栏中，搜索**Traffic Manager 配置文件**你想要修改，，然后单击结果中的流量管理器配置文件的名称的显示。
3. 在**Traffic Manager 配置文件**边栏选项卡，单击**概述**，在概述边栏选项卡中，单击**删除**，然后确认要删除流量管理器配置文件。

## <a name="next-steps"></a>后续步骤

* [添加一个终结点](traffic-manager-endpoints.md)
* [配置优先级路由方法](traffic-manager-configure-priority-routing-method.md)
* [配置地理路由方法](traffic-manager-configure-geographic-routing-method.md) 
* [配置加权路由方法](traffic-manager-configure-weighted-routing-method.md)
* [配置性能路由方法](traffic-manager-configure-performance-routing-method.md)
