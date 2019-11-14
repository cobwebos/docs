---
title: 配置多值流量路由-Azure 流量管理器
description: 本文介绍如何配置流量管理器以将流量路由到 A/AAAA 终结点。
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: allensu
ms.openlocfilehash: 3e5e6cb55b86df8a48f96771fb2436afa8acaa18
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040369"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>在流量管理器中配置多值路由方法

本文介绍如何配置多值流量路由方法。 使用多值流量路由方法，可以返回多个运行正常的终结点，并有助于提高应用程序的可靠性，因为客户端有更多选项可以重试，无需另外进行 DNS 查找。 多值路由仅针对使用 IPv4 或 IPv6 地址指定了所有端点的配置文件启用。 在收到此配置文件的查询时，会根据指定的可配置的最大返回计数返回所有运行正常的终结点。 

>[!NOTE]
> 此时仅外部类型的终结点支持使用 IPv4 或 IPv6 地址添加终结点，因此仅此类终结点支持多值路由。

## <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。
## <a name="create-a-resource-group"></a>创建资源组
创建流量管理器配置文件的资源组。
1. 在 Azure 门户的左侧窗格中，选择“资源组”。
2. 在“资源组”中，选择页面顶部的“添加”。
3. 在“资源组名称”中，键入名称 myResourceGroupTM1。 在“资源组位置”处，请选择“美国东部”，然后选择“确定”。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件
创建流量管理器配置文件，以便将用户流量定向到延迟最低的终结点。

1. 在屏幕左上方，选择“创建资源” > “网络” > “流量管理器配置文件” > “创建”。
2. 在“创建流量管理器配置文件”中输入或选择以下信息，接受其余设置的默认值，然后选择“创建”：
    
    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 名称                   | 此名称必须在 trafficmanager.net 区域中唯一，并可生成用于访问流量管理器配置文件的 DNS 名称 (trafficmanager.net)。                                   |
    | 路由方法          | 选择“多值”路由方法。                                       |
    | 订阅            | 选择订阅。                          |
    | 资源组          | 选择“myResourceGroupTM1”。 |
    | 位置                | 此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。                              |
   |        |           | 
  
   ![创建流量管理器配置文件](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

将两个 IP 地址作为外部终结点添加到在上一步中创建的多值流量管理器配置文件中。

1. 在门户的搜索栏中，搜索在前面部分创建的流量管理器配置文件名称，并在显示的结果中选择该配置文件。
2. 在“流量管理器配置文件”的“设置”部分单击“终结点”，然后单击“添加”。
3. 输入或选择以下信息，保留剩下的默认设置，然后选择“确定”：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 类型                    | 外部终结点                                   |
    | 名称           | myEndpoint1                                        |
    | 完全限定的域名 (FQDN) 或 IP           | 键入要添加到此流量管理器配置文件的终结点的公共 IP 地址                         |
    |        |           |

4. 重复步骤 2 和 3 以添加名为“myEndpoint2”的另一个终结点，在“完全限定的域名 (FQDN) 或 IP”处，输入第二个终结点的公共 IP 地址。
5. 添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

   ![添加流量管理器终结点](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>后续步骤

- 了解[加权流量路由方法](traffic-manager-configure-weighted-routing-method.md)。
- 了解[优先级路由方法](traffic-manager-configure-priority-routing-method.md)。
- 详细了解[性能路由方法](traffic-manager-configure-performance-routing-method.md)
- 了解[地理路由方法](traffic-manager-configure-geographic-routing-method.md)。


