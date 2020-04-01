---
title: 关于 Azure 边缘区域 - 预览
description: 了解微软提供的边缘计算产品。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 03/31/2020
ms.author: ganesr
ms.openlocfilehash: 4666022fc62fcb423906b46f0ff2274a2191f341
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437629"
---
# <a name="about-azure-edge-zones---preview"></a>关于 Azure 边缘区域 - 预览

Azure 边缘区域是 Microsoft Azure 提供的一系列产品，支持靠近用户的数据处理。 您可以将 VM、容器和其他选定的 Azure 服务部署到边缘区域，以满足应用程序的低延迟和高吞吐量要求。

边缘区域的典型用例方案包括：

- 机器人实时指挥与控制
- 使用人工智能和机器学习进行实时分析和推断
- 机器视觉
- 用于混合现实和 VDI 方案的远程渲染
- 沉浸式多人游戏
- 媒体流和内容交付
- 监视和安全

Azure 边缘区域有三个离散产品：

- Azure 边缘区域
- 带载波的 Azure 边缘区域
- Azure 专用边缘区域

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure 边缘区域

![边缘区域](./media/edge-zones-overview/edge-zones.png "边缘区域")

Azure 边缘区域是放置在远离 Azure 区域的人口中心中的 Azure 小范围扩展。 Azure 边缘区域支持 VM、容器和一组选定的 Azure 服务，这些服务允许您在接近最终用户时运行延迟敏感和吞吐量密集型应用程序。 Azure 边缘区域是 Microsoft 全局网络的一部分，在靠近用户的边缘区域运行的应用程序与在 Azure 区域内运行的完整 Azure 服务集之间提供安全、可靠和高带宽连接。 Azure 边缘区域由 Microsoft 拥有和运营，允许您使用同一组 Azure 工具和门户来管理服务并将其部署到边缘区域。

典型用例包括：

- 游戏和游戏流
- 媒体流和内容交付
- 使用人工智能和机器学习进行实时分析和推断
- 混合现实的渲染

Azure 边缘区域将在以下城域中可用：

- 纽约州纽约市
- 加利福尼亚州洛杉矶
- 弗罗里达州迈阿密

[有关详细信息，与边缘区域团队](https://aka.ms/EdgeZones)联系。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>带载波的 Azure 边缘区域

![带载波的边缘区域](./media/edge-zones-overview/edge-carrier.png "带载波的边缘区域")

具有载波的 Azure 边缘区域是放置在移动运营商数据中心中填充中心的 Azure 小占用空间扩展。 具有运营商基础结构的 Azure 边缘区域距离移动运营商的 5G 网络一跃而过，为移动设备的应用程序提供 10 毫秒以下的延迟。 具有运营商的 Azure 边缘区域部署在移动运营商的数据中心中，并连接到 Microsoft 全球网络。 它们在用户附近运行的应用程序和在 Azure 区域内运行的完整 Azure 服务集之间提供安全、可靠和高带宽的连接。 开发人员可以使用同一组熟悉的工具在边缘区域中构建和部署服务。

典型用例包括：

- 游戏和游戏流
- 媒体流和内容交付
- 使用人工智能和机器学习进行实时分析和推断
- 混合现实的渲染
- 互联汽车
- 远程

边缘区域将与以下运营商合作提供：

- AT&T（亚特兰大、达拉斯和洛杉矶）
- 埃泰萨尔特
- Rogers
- Vodafone

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 专用边缘区域

![专用边缘区域](./media/edge-zones-overview/private-edge.png "专用边缘区域")

Azure 专用边缘区域是放置在本地的 Azure 的较小占用空间扩展。 Azure 专用边缘区域基于[Azure 堆栈边缘](https://azure.microsoft.com/products/azure-stack/edge/)平台，并且允许对本地部署的计算和存储服务进行低延迟访问。 专用边缘区域还允许您将虚拟化网络功能 （VNF）（如移动数据包核心、路由器、防火墙和 SD-WAN 设备）以及 ISVS 的应用程序并行部署，以及本地虚拟机和容器作为[Azure 托管应用程序](https://azure.microsoft.com/services/managed-applications/)。 Azure 专用边缘区域附带了云原生业务流程解决方案，允许您从 Azure 门户管理虚拟化网络功能 （VNF） 和应用程序的生命周期。

Azure 专用边缘区域允许您使用用于在 Azure 中生成和部署应用程序的相同熟悉工具在本地开发和部署应用程序。 您还可以运行专用移动网络（专用 LTE、专用 5G）、安全功能（如防火墙），并使用同一专用边缘区域设备上的 SD-WAN 设备跨多个分支和 Azure 扩展本地网络，并从 Azure 管理它们。

典型用例包括：

- 机器人实时指挥与控制
- 使用人工智能和机器学习进行实时分析和推断
- 机器视觉
- 用于混合现实和 VDI 方案的远程渲染
- 监视和安全

我们拥有由 VNF 供应商、ISV 和 MSP 合作伙伴组成的丰富生态系统，能够使用专用边缘区域实现端到端解决方案。 [有关详细信息，联系专用边缘区域团队](https://aka.ms/EdgeZonesPartner)。

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>专用边缘区域 - 合作伙伴

![专用边缘区域合作伙伴](./media/edge-zones-overview/partners.png "专用边缘区域合作伙伴")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>虚拟化网络功能

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>移动网络的虚拟化进化数据包核心 （vEPC）

- [已确认网络](https://www.affirmednetworks.com/)
- [德鲁伊软件](https://www.druidsoftware.com/)
- [埃佩托](https://www.expeto.io/)
- [马韦尼尔](https://mavenir.com/)
- [元开关](https://www.metaswitch.com/)
- [诺基亚数字自动化云](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>移动无线电合作伙伴

- [通讯鲁斯](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 供应商

- [净铸造](https://netfoundry.io/)
- [来自诺基亚的 Nuage 网络](https://www.nuagenetworks.net/)
- [VMware SD-WAN 由 Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>路由器供应商

- [Arista](https://www.arista.com/)

[联系专用边缘区域团队](https://aka.ms/EdgeZonesPartner)，了解有关如何成为合作伙伴的更多信息。

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火墙供应商

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>托管解决方案提供商 - 移动运营商和全球系统集成商

| 全球 SI 和运营商 | 移动运营商 |
| --- | --- |
| 阿姆多克斯                       | 埃泰萨尔特             |
| 美国塔               | Rogers               |
| 埃佩托                       | 辛特尔              |
| 联合无线           | Vodafone             |
| Infosys                      |      *                |
| 科技马辛德拉                |      *                |

[联系专用边缘区域团队](https://aka.ms/EdgeZonesPartner)，了解有关如何成为合作伙伴的更多信息。

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>专用边缘区域 - 解决方案

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>私人边缘区域上的专用移动网络

![私人边缘区域上的专用移动网络](./media/edge-zones-overview/mobile-networks.png "私人边缘区域上的专用移动网络")

您现在可以在专用边缘区域部署专用移动网络。 专用移动网络可实现超低延迟、高容量以及业务关键型应用所需的可靠和安全无线网络。 专用移动网络可以实现诸如仓库中自动引导车辆 （AGV） 的指挥和控制、智能工厂中的机器人与增强型房地产之间的实时通信以及虚拟现实边缘应用等场景。

虚拟化进化的数据包核心（vEPC）网络功能构成了专用移动网络的大脑。 您现在可以在专用边缘区域部署 vEPC。 有关在专用边缘区域可用的 vEPC 合作伙伴的列表，请参阅[vEPC ISV](#vEPC)。

在专用边缘区域部署专用移动网络解决方案需要其他组件，如移动接入点、SIM 卡和其他 VNF（如路由器）。 访问许可或未经许可的频谱对于设置专用移动网络至关重要。 此外，您可能需要关于射频规划、物理布局、安装和支持的帮助。 有关合作伙伴列表，请参阅[移动无线电合作伙伴](#mobile-radio)。

Microsoft 提供了一个合作伙伴生态系统，可帮助处理此过程的所有方面 - 从规划网络、购买所需设备、设置硬件，到从 Azure 管理配置。 通过一组与 Microsoft 紧密集成的经过验证的合作伙伴，您可以放心，解决方案将可靠且易于使用。 您可以专注于核心方案，同时依靠 Microsoft 及其合作伙伴帮助解决其余方案。

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>私有边缘区域上的 SD-WAN

![私有边缘区域上的 SD-WAN](./media/edge-zones-overview/sd-wan.png "私有边缘区域上的 SD-WAN")
 
SD-WAN 作为一项技术，可让您创建企业级广域网络 （WAN），具有更高的带宽、对云的高性能访问、服务插入、可靠性、策略管理和广泛的网络可见性。 SD-WAN 提供由冗余中央控制器精心编排的无缝分支机构连接，拥有成本更低。
私有边缘区域的 SD-WAN 允许您从以资本支出为中心的模型转向软件即服务 （SaaS） 模型，以减少 IT 预算。 您可以使用您选择的 SD-WAN 合作伙伴协调器或控制器来启用新服务并立即在整个网络中传播它们。

## <a name="next-steps"></a>后续步骤

有关详细信息，可联系以下团队：

* [边缘区域团队](https://aka.ms/EdgeZones)
* [私人边缘区域团队 - 成为合作伙伴](https://aka.ms/EdgeZonesPartner)
