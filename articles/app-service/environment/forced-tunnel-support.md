---
title: 将 Azure 应用服务环境配置为采用强制隧道
description: 在通过强制隧道传输出站流量时，使应用服务环境能够正常运行
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 082275e2acd81e34c057f863651528eb46e8501e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114946"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>使用强制隧道配置应用服务环境

应用服务环境 (ASE) 是部署在客户的 Azure 虚拟网络中的 Azure 应用服务。 许多客户使用 VPN 或 Azure ExpressRoute 连接将其 Azure 虚拟网络配置为本地网络的扩展。 强制隧道是指将本应发往 Internet 的流量重定向到 VPN 或虚拟设备。 这通常是安全要求的一部分，目的是检查和审核所有出站流量。 

ASE 具有许多外部依赖项，详见[应用服务环境网络体系结构][network]文档中的说明。 通常情况下，所有 ASE 出站依赖项流量必须通过 ASE 中预配的 VIP。 如果更改了出入 ASE 的流量的路由而没有遵循以下说明，则 ASE 会停止运行。

在 Azure 虚拟网络中，路由是基于最长前缀匹配 (LPM) 实现的。 如果有多个路由的 LPM 匹配情况相同，则按以下顺序根据路由源来选择路由：

* 用户定义的路由 (UDR)
* BGP 路由（当使用 ExpressRoute 时）
* 系统路由

若要详细了解虚拟网络中的路由，请参阅[用户定义的路由和 IP 转发][routes]。 

如果需要将 ASE 出站流量路由到其他地方而不是直接路由到 Internet，则有以下选择：

* 让 ASE 获得直接 Internet 访问权限
* 将 ASE 子网配置为忽略 BGP 路由
* 将 ASE 子网配置为使用 Azure SQL 和 Azure 存储的服务终结点
* 将自己的 IP 添加到 ASE Azure SQL 防火墙

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>启用应用服务环境，获得直接访问 Internet 的权限

若要让 ASE 在 Azure 虚拟网络配置了 ExpressRoute 的情况下也直接转到 Internet，可执行以下操作：

* 将 ExpressRoute 配置为播发 0.0.0.0/0。 默认情况下，它路由所有本地出站流量。
* 创建地址前缀为 0.0.0.0/0、下一跃点类型为 Internet 的 UDR，将其应用到 ASE 子网。

如果执行这两项更改，则不会强制由应用服务环境子网发往 Internet 的流量通过 ExpressRoute 连接。

如果网络已将流量路由到本地，则需创建用于托管 ASE 的子网，并为其配置 UDR，然后才能尝试部署该 ASE。  

> [!IMPORTANT]
> UDR 中定义的路由必须足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 上述示例使用了广泛的 0.0.0.0/0 地址范围。 因此很困意外地被使用更具体地址范围的路由播发重写。
>
> 交叉播发从公共对等路径到专用对等路径的路由的 ExpressRoute 配置不支持应用服务环境。 已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的路由播发。 这些播发包含大量的 Microsoft Azure 地址范围。 如果这些地址范围在专用对等路径上交叉播发，则来自应用服务环境子网的所有出站网络数据包均路由至客户的本地网络基础结构。 默认情况下，应用服务环境不支持此网络流。 一个解决方法是停止公共对等路径到专用对等路径的交叉播发路由。 另一种解决方法是使应用服务环境能够在强制隧道配置中正常工作。

![直接 Internet 访问][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>将 ASE 子网配置为忽略 BGP 路由 ## 

可将 ASE 子网配置为忽略所有 BGP 路由。  如果采用这种配置，ASE 将可以访问其依赖项，而不会出现任何问题。  但是，需要创建 UDR 才能让应用访问本地资源。

将 ASE 子网配置为忽略 BGP 路由：

* 创建 UDR 并将其分配到 ASE 子网（如果没有 UDR）。
* 在 Azure 门户中，打开分配到 ASE 子网的路由表的 UI。  选择“配置”。  将 BGP 路由传播设置为“已禁用”。  单击“保存”。 [创建路由表][routetable]文档介绍了如何关闭此设置。

执行此操作后，应用程序不再能够访问本地资源。 若要解决此问题，请编辑分配到 ASE 子网的 UDR，并添加本地地址范围的路由。 “下一跃点类型”应设置为“虚拟网络网关”。 


## <a name="configure-your-ase-with-service-endpoints"></a>为 ASE 配置服务终结点 ##

 > [!NOTE]
   > 使用 SQL 的服务终结点不适用于 US Government 区域中的 ASE。  以下信息仅在 Azure 公共区域有效。  

若要路由来自 ASE 的所有出站流量（到 Azure SQL 和 Azure 存储的除外），请执行以下步骤：

1. 创建一个路由表，将其分配给 ASE 子网。 若要查找与区域相匹配的地址，请参阅[应用服务环境管理地址][management]。 为下一跃点为 Internet 的那些地址创建路由。 之所以需要这个，是因为应用服务环境入站管理流量必须从发送到的地址进行回复。   

2. 为 ASE 子网启用 Azure SQL 和 Azure 存储的服务终结点。  完成此步骤以后，即可使用强制隧道来配置 VNet。

若要在虚拟网络中创建 ASE，而该虚拟网络已配置为将所有流量路由到本地，则需使用资源管理器模板来创建 ASE。  无法通过门户将 ASE 创建到预先存在的子网中。  若要将 ASE 部署到 VNet 中，而该 VNet 已配置为将所有出站流量路由到本地，则需使用可指定预先存在的子网的资源管理器模板来创建 ASE。 若要详细了解如何使用模板来部署 ASE，请阅读[使用模板创建应用服务环境][template]。

可以通过服务终结点将多租户服务的访问权限限制给一组 Azure 虚拟网络和子网。 若要详细了解服务终结点，可参阅[虚拟网络服务终结点][serviceendpoints]文档。 

在资源上启用服务终结点时，有些已创建路由的优先级高于所有其他路由。 如果将服务终结点与强制隧道 ASE 配合使用，则 Azure SQL 和 Azure 存储管理流量不会通过强制隧道进行重定向。 其他 ASE 依赖项流量会通过强制隧道重定向，不能丢失，否则 ASE 会功能失常。

在包含 Azure SQL 实例的子网上启用服务终结点时，所有与该子网有连接的 Azure SQL 实例必定会启用服务终结点。 如果需要从同一子网访问多个 Azure SQL 实例，则不能在一个 Azure SQL 实例上启用服务终结点，而在另一个实例上不启用。  Azure 存储的表现与 Azure SQL 不同。  对 Azure 存储启用服务终结点时，可以锁定从子网对该资源进行的访问，但仍可访问其他 Azure 存储帐户，即使这些帐户未启用服务终结点。  

如果为强制隧道配置网络筛选设备，则请记住，除了 Azure SQL 和 Azure 存储，ASE 还有依赖项。 必须允许流向这些依赖项的流量，否则 ASE 会功能失常。

![使用服务终结点的强制隧道][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>将自己的 IP 添加到 ASE Azure SQL 防火墙 ##

若要让来自 ASE 的所有出站流量（到 Azure 存储的除外）进入隧道，请执行以下步骤：

1. 创建一个路由表，将其分配给 ASE 子网。 若要查找与区域相匹配的地址，请参阅[应用服务环境管理地址][management]。 为下一跃点为 Internet 的那些地址创建路由。 之所以需要这个，是因为应用服务环境入站管理流量必须从发送到的地址进行回复。 

2. 为 ASE 子网启用 Azure 存储的服务终结点

3. 获取可供所有从应用服务环境到 Internet 的出站流量使用的地址。 如果在本地路由流量，则这些地址为 NAT 或网关 IP。 若要通过 NVA 路由应用服务环境出站流量，则出口地址为 NVA 的公共 IP。

4. _若要在现有的应用服务环境中设置传出地址：_ 请转到 resource.azure.com，再转到 Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>。 然后即可看到描述应用服务环境的 JSON 代码。 确保代码的顶部显示“读/写”。 选择“编辑”。 向下滚动到底部。 将“userWhitelistedIpRanges”值从“null”更改为类似于以下内容的值。 使用要设置为出口地址范围的地址。 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   选择顶部的“PUT”。 此选项会触发应用服务环境的缩放操作，并对防火墙进行调整。

_若要使用传出地址创建 ASE_：请按[使用模板创建应用服务环境][template]中的说明操作，拉取相应的模板。  编辑 azuredeploy.json 文件中的 "resources" 节，但 "properties" 块除外，并添加一行，用于 **userWhitelistedIpRanges**（含值）。

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

进行这些更改后，即可将流量直接从 ASE 发送到 Azure 存储，并且可以从 ASE 的 VIP 之外的其他地址访问 Azure SQL。

   ![使用 SQL 允许列表的强制隧道][3]

## <a name="preventing-issues"></a>防止问题 ##

如果 ASE 与其依赖项之间的通信中断，ASE 会进入不正常状态。  如果保持不正常状态的时间过长，ASE 会暂停。 若要取消 ASE 的暂停，请按 ASE 门户中的说明操作。

除了直接中断通信，还可以引入过高的延迟，对 ASE 造成负面影响。 如果 ASE 与本地网络相距过远，则可能会造成延迟过高。  例如，如果需要跨洋过洲来访问本地网络，则表明相距过远。 如果出现 Intranet 拥塞或存在出站带宽约束，则也可能会导致延迟。


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
