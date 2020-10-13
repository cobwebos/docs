---
title: 部署流量管理器以平衡 Azure VMware 解决方案 (AVS) 工作负荷
description: 了解如何将流量管理器与 Azure VMware 解决方案 (AVS) 集成，以在不同区域中跨多个终结点平衡应用程序工作负载。
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: d461cc444c60e1907a34a08c68139446301c133c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579598"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>部署流量管理器以平衡 Azure VMware 解决方案 (AVS) 工作负荷

本文逐步讲解如何将流量管理器与 Azure VMware 解决方案集成 (AVS) ，以跨多个终结点平衡应用程序工作负荷。 我们将探讨这样一种情况：流量管理器会将三个应用程序网关之间的流量定向到多个 AVS 区域（美国西部、西欧和本地）。 

Azure 流量管理器是一种基于 DNS 的流量负载均衡器，可让你以最佳方式将流量分配到全球 Azure 区域内的服务。 它将在 Azure 运行的工作负荷和外部公共终结点之间对应用程序流量进行负载均衡。 有关流量管理器的详细信息，请参阅 [什么是流量管理器？](../traffic-manager/traffic-manager-overview.md)

首先查看 [先决条件](#prerequisites) ;接下来，我们将指导完成以下过程：

> [!div class="checklist"]
> * 验证应用程序网关的配置
> * 验证 NSX 段的配置
> * 创建流量管理器配置文件
> * 将外部终结点添加到流量管理器配置文件

## <a name="topology"></a>拓扑

如下图所示，Azure 流量管理器在区域终结点之间的 DNS 级别为应用程序提供负载均衡。 应用程序网关将后端池成员配置为 IIS 服务器，并将其作为 AVS 外部终结点进行引用。

通过虚拟网络连接到两个 AVS 私有云区域（美国西部和西欧）和美国东部的本地服务器之间的连接，使用 ExpressRoute 网关。   

![流量管理器与 AVS 的集成](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>必备条件

- 三个虚拟机配置为在不同的 AVS 区域中运行的 Microsoft IIS 服务器：美国西部、西欧以及本地。 

- 在美国西部、西欧和本地使用外部终结点的应用程序网关。

- 具有 internet 连接的主机用于验证。 

## <a name="verify-configuration-of-your-application-gateways"></a>验证应用程序网关的配置

[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/) 是第7层 web 流量负载均衡器，可用于管理 web 应用程序的流量。 有关应用程序网关的详细信息，请参阅 [什么是 Azure 应用程序的网关？](../application-gateway/overview.md) 

在此方案中，将三个应用程序网关实例配置为外部 AVS 终结点。 应用程序网关具有配置为后端池成员的 AVS 虚拟机，用于对传入第7层请求进行负载均衡。  (若要了解如何使用 AVS 虚拟机作为后端池配置应用程序网关，请参阅 [使用 Azure 应用程序的网关保护 Azure VMware 解决方案上的 web 应用](protect-azure-vmware-solution-with-application-gateway.md)。 )   

以下步骤验证是否正确配置了应用程序网关。

1. 打开 Azure 门户，然后选择 " **应用程序网关** "，查看当前应用程序网关的列表。 

    对于此方案，我们配置了三个应用程序网关：
    - AVS-GW-WUS
    - AVS-EUS (本地) 
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="应用程序网关的列表。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. 选择以前部署的应用程序网关之一。 此时会打开一个窗口，其中显示了应用程序网关上的各种信息。 选择 " **后端池** "，验证某个后端池的配置。

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="应用程序网关的列表。" lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. 在这种情况下，我们会看到一个虚拟机后端池成员配置为具有 172.29.1.10 IP 地址的 web 服务器。
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="应用程序网关的列表。":::

    同样，可以验证其他应用程序网关和后端池成员的配置。 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>验证 NSX 段的配置

在 NSX-T Manager 中创建的网络段用作 vCenter 中虚拟机的网络。 有关详细信息，请参阅教程： [在 Azure VMware 解决方案中创建 NSX-T 网段 (AVS) ](tutorial-nsx-t-network-segment.md)。

在我们的方案中，在附加了后端池成员虚拟机的 AVS 环境中配置了 NSX-T 段。

1. 选择 " **段** " 可查看已配置的段。 在这种情况下，我们会看到 Contoso-segment1 连接到 T01 网关，后者是第1层灵活路由器。

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="应用程序网关的列表。":::    

2. 选择 **第1层网关** ，查看具有链接段数的第1层网关的列表。 选择链接到 T01 的段。 此时会打开一个窗口，其中显示了在第01层路由器上配置的逻辑接口。 这将充当连接到段的后端池成员虚拟机的网关。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="应用程序网关的列表。":::    

3. 在 VM vSphere 客户端中，选择要查看其详细信息的虚拟机。 请注意，它的 IP 地址与我们在上一节的步骤3中看到的内容相匹配：172.29.1.10。

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="应用程序网关的列表。" 段连接。

## <a name="create-your-traffic-manager-profile"></a>创建流量管理器配置文件

1. 登录到 [Azure 门户](https://rc.portal.azure.com/#home)。 在 " **Azure 服务 > 网络**" 下，选择 " **流量管理器配置文件**"。

2. 选择 " **+ 添加** " 创建新的流量管理器配置文件。
 
3. 提供配置文件名称、路由方法 (在此方案中将使用加权;请参阅 [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)) 、订阅和资源组，并选择 " **创建**"。

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>将外部终结点添加到流量管理器配置文件

1. 从搜索结果窗格中选择流量管理器配置文件，依次选择 " **终结点** " 和 " **+ 添加**"。

2. 输入所需的详细信息：类型、名称、完全限定的域名 (FQDN) 或 IP，权重 (在此方案中，我们将为每个终结点) 指定权重1。 选择“添加”  。

   :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="应用程序网关的列表。" 下的 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="应用程序网关的列表。"::: 

4. 在浏览器中粘贴 DNS 名称 URL。 以下屏幕截图显示定向到西欧区域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="应用程序网关的列表。"::: 

5. 刷新浏览器。 以下屏幕截图显示流量现在定向到美国西部区域中的另一组后端池成员。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="应用程序网关的列表。"::: 

6. 再次刷新浏览器。 以下屏幕截图显示流量现在定向到本地的后端池成员组。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="应用程序网关的列表。":::

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [使用 Azure VMware 解决方案上的 Azure 应用程序网关 (AVS) ](protect-azure-vmware-solution-with-application-gateway.md)
- [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)
- [在 Azure 中组合负载平衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [测量流量管理器性能](../traffic-manager/traffic-manager-performance-considerations.md)
