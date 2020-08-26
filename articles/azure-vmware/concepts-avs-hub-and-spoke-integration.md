---
title: 概念-在中心辐射型体系结构中集成 Azure VMware 解决方案部署
description: 了解有关在 Azure 上的现有或新的中心和辐射型体系结构中集成 Azure VMware 解决方案部署的建议。
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684472"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>在中心和辐射型体系结构中集成 Azure VMware 解决方案

本文介绍如何在 Azure 上的现有或新的 [中心和辐射型体系结构](/azure/architecture/reference-architectures/hybrid-networking/shared-services) 中集成 Azure VMware 解决方案部署。 

中心辐射型方案假设混合云环境中的工作负荷已开启：

* 使用 IaaS 或 PaaS 服务的本机 Azure
* Azure VMware 解决方案 
* 本地 vSphere

## <a name="architecture"></a>体系结构

*中心*是一种 Azure 虚拟网络，它充当本地和 Azure VMware 解决方案私有云的中心连接点。 *轮辐*是与中心对等互连的虚拟网络，用于启用跨虚拟网络通信。

本地数据中心、Azure VMware 解决方案私有云和中心之间的流量通过 ExpressRoute 连接。 轮辐虚拟网络通常包含基于 IaaS 的工作负载，但可以具有 PaaS 服务（如 [应用服务环境](../app-service/environment/intro.md)）、直接与虚拟网络集成或启用了 [Azure 专用链接](../private-link/index.yml) 的其他 PaaS 服务。 

此图显示了通过 ExpressRoute 连接到本地和 Azure VMware 解决方案的 Azure 中的中心和分支部署的示例。

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Azure VMware 解决方案中心和分支集成部署" border="false":::

该体系结构包括以下主要组件：

-   **本地站点：** 客户本地数据中心 (s) 通过 ExpressRoute 连接连接到 Azure。

-   **Azure VMware 解决方案私有云：** 由一个或多个 vSphere 群集构成的 Azure VMware 解决方案 SDDC，每个群集最多包含16个节点。

-   **ExpressRoute 网关：** 启用 Azure VMware 解决方案私有云、本地网络、中心虚拟网络上的共享服务和在辐射虚拟网络上运行的工作负载之间的通信。

    > [!NOTE]
    > **S2S VPN 注意事项：** 对于 Azure VMware 解决方案生产部署，不支持 Azure S2S，因为 HCX 的网络要求。 但是，对于不需要 HCX 的 PoC 或非生产部署，可以使用它。

-   **中心虚拟网络：** 作为与本地网络和 Azure VMware 解决方案私有云的连接的中心点。

-   **辐射虚拟网络**

    -   **IaaS 分支：** IaaS 辐射托管基于 Azure IaaS 的工作负载，包括 VM 可用性集和虚拟机规模集，以及相应的网络组件。

    -   **PaaS 分支：** PaaS 分支使用专用寻址来托管 Azure PaaS 服务，感谢 [专用终结点](../private-link/private-endpoint-overview.md) 和 [专用链接](../private-link/private-link-overview.md)。

-   **Azure 防火墙：** 充当辐射、本地和 Azure VMware 解决方案之间的通信的中心部分。

-   **应用程序网关：** 公开和保护在 Azure IaaS/PaaS 或 Azure VMware 解决方案虚拟机 (Vm) 上运行的 web 应用。 它与其他服务（例如 API 管理）集成。

## <a name="network-and-security-considerations"></a>网络和安全注意事项

ExpressRoute 连接使流量能够在本地、Azure VMware 解决方案和 Azure 网络结构之间流动。 Azure VMware 解决方案使用 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) 来实现这种连接。

本地连接还可以使用 ExpressRoute Global Reach，但这不是必需的。

* **本地到 Azure VMware 解决方案流量流**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="本地到 Azure VMware 解决方案流量流" border="false":::


* **Azure VMware 解决方案到中心 VNET 的通信流**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware 解决方案到中心虚拟网络流量流" border="false":::


有关 azure VMware 解决方案网络和互连的详细信息，请 [参阅 Azure Vmware 解决方案产品文档](./concepts-networking.md)。

### <a name="traffic-segmentation"></a>流量分段

[Azure 防火墙](../firewall/index.yml) 是中心和辐射拓扑的核心拓扑，部署在中心虚拟网络上。 使用 Azure 防火墙或其他受 Azure 支持的网络虚拟设备来建立流量规则，并分段不同辐射、本地和 Azure VMware 解决方案工作负载之间的通信。

创建路由表以将流量定向到 Azure 防火墙。  对于辐射虚拟网络，请创建一个将默认路由设置为 Azure 防火墙内部接口的路由，这种情况下，当虚拟网络中的工作负荷需要访问 Azure VMware 解决方案地址空间时，防火墙可以对其进行评估，并应用相应的流量规则以允许或拒绝它。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="创建路由表以将流量定向到 Azure 防火墙":::


> [!IMPORTANT]
> 不支持 **GatewaySubnet** 设置上地址前缀为 0.0.0.0/0 的路由。

针对相应的路由表设置特定网络的路由。 例如，路由从本地到达 Azure VMware 解决方案管理和工作负荷 IP 前缀，反之亦然，将所有流量从本地路由到 Azure VMware 解决方案私有云，通过 Azure 防火墙。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="针对相应的路由表设置特定网络的路由":::

使用轮辐和中心内的网络安全组创建更精细的流量分段的二级流量分段。 


### <a name="application-gateway"></a>应用程序网关

已使用在作为后端池的 Azure VMware 解决方案虚拟机上运行的 web 应用来测试 Azure 应用程序网关 V1 和 V2。 应用程序网关目前是唯一受支持的方法，可将 Azure VMware 解决方案 Vm 上运行的 web 应用公开到 internet。 它也可以安全地向内部用户公开应用程序。

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="使用网络安全组的第二级流量分段" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox 和 Azure 堡垒

使用 Jumpbox 访问 Azure VMware 解决方案环境，该环境是在中心虚拟网络中的共享服务子网中部署的 Windows 10 或 Windows Server VM。

作为最佳安全方案，请在中心虚拟网络中部署 [Microsoft Azure 堡垒](../bastion/index.yml) 服务。 Azure 堡垒提供对 Azure 中部署的 Vm 的无缝 RDP 和 SSH 访问，无需为这些资源预配公共 IP 地址。 预配 Azure 堡垒服务后，可以从 Azure 门户访问所选 VM。 建立连接后，会打开一个新的选项卡，显示 Jumpbox 桌面，并从该桌面访问 Azure VMware 解决方案私有云管理平面。

> [!IMPORTANT]
> 不要向 Jumpbox VM 提供公共 IP 地址，也不要向公共 internet 公开 3389/TCP 端口。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure 堡垒中心虚拟网络" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 解析注意事项

Azure DNS 分辨率有两个可用的选项：

-   使用 Azure Active Directory (Azure AD 在中心部署的) 域控制器 (作为名称服务器 [) 中所](#identity-considerations) 述。

-   部署和配置 Azure DNS 专用区域。

最佳方法是将两者结合起来，为 Azure VMware 解决方案、本地和 Azure 提供可靠的名称解析。

作为一般的设计建议，在这种情况下，请使用现有的 Azure DNS 基础结构 (，将 Active Directory 集成的) DNS 部署到至少部署在中心虚拟网络中的两个 Azure Vm，并在辐射虚拟网络中进行配置，以在 DNS 设置中使用这些 Azure DNS 服务器。

Azure 专用 DNS 仍可用于 Azure 专用 DNS 区域链接到虚拟网络的位置，而 DNS 服务器则用作混合解析程序，并将条件转发到运行 DNS 的本地/Azure VMware 解决方案，以利用客户 Azure 专用 DNS 基础结构。

Azure DNS 专用区域需要考虑几个注意事项：

* 应为 Azure DNS 启用自动注册，以便为在辐射虚拟网络中部署的 Vm 自动管理 DNS 记录的生命周期。
* 虚拟网络可链接到的最大专用 DNS 区域数仅为自动注册。
* 虚拟网络可链接到的专用 DNS 区域的最大数目为1000，且不启用自动注册。

本地和 Azure VMware 解决方案服务器可以配置有条件转发器，以便在 azure 中为 Azure 专用 DNS 区域解析 Vm。

## <a name="identity-considerations"></a>标识注意事项

出于标识目的，最佳方法是在中心部署至少一个 AD 域控制器，使用共享服务子网，理想情况下使用共享服务子网或 VM 可用性集。 请参阅 [Azure 体系结构中心](/azure/architecture/reference-architectures/identity/adds-extend-domain) 将本地 AD 域扩展到 Azure。

此外，在 Azure VMware 解决方案端部署另一个域控制器，在 vSphere 环境中充当标识和 DNS 源。

对于 vCenter 和 SSO，请在 " **管理 \> 标识 \> 标识源**" 中设置 Azure 门户中的标识源。

作为推荐的最佳做法，将 [AD 域与 Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad)集成。

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
