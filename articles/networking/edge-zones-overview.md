---
title: 关于 Azure Edge 区域预览
description: 了解 Microsoft 的 edge 计算产品： Azure 边缘区域。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 07/07/2020
ms.author: ganesr
ms.openlocfilehash: 68aa3342ab09be73a82f4f896ffdff99d15a5350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329560"
---
# <a name="about-azure-edge-zone-preview"></a>关于 Azure Edge 区域预览

Azure 边缘区域是 Microsoft Azure 中的一系列产品，可使数据处理靠近用户。 你可以将 Vm、容器和其他所选 Azure 服务部署到边缘区域，以解决应用程序的低延迟和高吞吐量要求。

边缘区域的典型用例方案包括：

- 自动装置中的实时命令和控制。
- 通过人工智能和机器学习进行实时分析和推断。
- 计算机视觉。
- 混合现实和 VDI 方案的远程呈现。
- 沉浸式多玩家游戏。
- 媒体流式处理和内容传递。
- 监视和安全性。

有三种类型的 Azure 边缘区域：

- Azure Edge Zone
- 带有电信公司的 Azure 边缘区域
- Azure 专用边缘区域

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Azure Edge Zone")

Azure 边缘区域是位于离 Azure 区域远离的人口中心中的小型 Azure 扩展。 Azure 边缘区域支持 Vm、容器和所选的一组 Azure 服务，可让你对最终用户近时间运行与吞吐量相关的应用程序。 Azure 边缘区域是 Microsoft 全球网络的一部分。 它们在靠近用户的边缘区域中运行的应用程序之间提供安全、可靠、高带宽的连接。 它们提供了在 Azure 区域内运行的一整套 Azure 服务。 Azure 边缘区域由 Microsoft 拥有和操作。 可以使用相同的 Azure 工具集和相同的门户来管理和部署服务到边缘区域。

典型用例包括：

- 游戏和游戏流式处理。
- 媒体流式处理和内容传递。
- 通过人工智能和机器学习进行实时分析和推断。
- 混合现实的呈现。

以下大都市区域将提供 Azure 边缘区域：

- 纽约州纽约市
- 加利福尼亚州洛杉矶
- 弗罗里达州迈阿密

有关详细信息，[请联系边缘区域团队](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>带有电信公司的 Azure 边缘区域

![带有电信公司的边缘区域](./media/edge-zones-overview/edge-carrier.png "带有电信公司的边缘区域")

带有电信公司的 azure 边缘区域是 Azure 的小型空间扩展，位于人口中心的移动运营商的数据中心。 与运营商基础结构一起提供的 Azure 边缘区域远离移动运营商的5G 网络。 此位置为移动设备中的应用程序提供不到10毫秒的延迟。

带有电信公司的 Azure 边缘区域部署在移动运营商的数据中心中，并连接到 Microsoft 全球网络。 它们在运行接近用户的应用程序之间提供安全、可靠、高带宽的连接。 它们提供了在 Azure 区域内运行的一整套 Azure 服务。 开发人员可以使用一组熟悉的工具来构建服务并将其部署到边缘区域。

典型用例包括：

- 游戏和游戏流式处理。
- 媒体流式处理和内容传递。
- 通过人工智能和机器学习进行实时分析和推断。
- 混合现实的呈现。
- 已连接汽车。
- 远程-医科。

与以下运算符合作将提供边缘区域：

- &T (亚特兰大、达拉斯和洛杉矶) 

与5G 网络连接的优化和可缩放应用程序合作的 Isv 现在可以使用 Azure 边缘区域的新洛杉矶预览位置，在生成和试验超高延迟平台、移动和连接方案时，可以使用&T。 注册早期使用者计划以利用安全的高带宽连接。

有关详细信息，[请联系边缘区域团队](https://aka.ms/EdgeZones)。

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 专用边缘区域

![专用边缘区域](./media/edge-zones-overview/private-edge.png "专用边缘区域")

Azure 专用边缘区域是 Azure 的小型空间扩展，位于本地。 Azure 专用边缘区域基于 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) 平台。 它支持对本地部署的计算和存储服务进行低延迟访问。 专用边缘区域还允许您从 Isv 和虚拟化网络功能部署应用程序 (VNFs) 作为 [Azure 托管应用程序](https://azure.microsoft.com/services/managed-applications/) 以及本地的虚拟机和容器。 这些 VNFs 可以包括移动数据包核心、路由器、防火墙和 SD WAN 设备。 Azure 专用边缘区域附带了一个云本机业务流程解决方案，可让你从 Azure 门户管理 VNFs 和应用程序的生命周期。

使用 azure 专用边缘区域，你可以使用在 Azure 中构建和部署应用程序时所用的熟悉工具，在本地开发和部署应用程序。

它还允许：

-  (专用 LTE) 上运行专用移动网络。
- 实现安全功能，如防火墙。
- 通过在同一专用边缘区域设备上使用 SD WAN 设备并从 Azure 管理这些网络，跨多个分支和 Azure 扩展本地网络。

典型用例包括：

- 自动装置中的实时命令和控制。
- 利用人工智能和机器学习进行实时分析和推断。
- 计算机视觉。
- 混合现实和 VDI 方案的远程呈现。
- 监视和安全性。

我们有丰富的 VNF 供应商、Isv 和 MSP 合作伙伴的生态系统，可启用使用专用边缘区域的端到端解决方案。 有关详细信息，[请联系专用边缘区域团队](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>专用边缘区域伙伴

![专用边缘区域伙伴](./media/edge-zones-overview/partners.png "专用边缘区域伙伴")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>虚拟化网络功能 (VNFs) 

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>移动网络的虚拟化演化数据包核心 (vEPC) 

- [Affirmed 网络](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid 软件](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia 数字自动化云](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>移动广播合作伙伴

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 供应商

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [从 Nokia Nuage 网络](https://www.nuagenetworks.net/)
- [反向网络](https://www.versa-networks.com/)
- [VMware SD-WAN by Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>路由器供应商

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火墙供应商

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>托管解决方案提供商：移动运营商和全球系统集成商 (GSIs) 

| GSIs 和运算符 | 移动运营商 |
| --- | --- |
| Amdocs                       | Etisalat             |
| 美洲塔式               | NTT 通信   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| 联合无线           | SK 电信           |
| Infosys                      | Telefonica           |
| 技术 Mahindra                | Telstra              |
|                              | Vodafone             |

[联系专用边缘区域团队](https://aka.ms/EdgeZonesPartner) 了解有关如何成为合作伙伴的信息。

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>专用边缘区域解决方案

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>专用边缘区域上的专用移动网络

![专用边缘区域上的专用移动网络](./media/edge-zones-overview/mobile-networks.png "专用边缘区域上的专用移动网络")

你现在可以在专用边缘区域上部署专用移动网络。 专用移动网络实现了紧急低延迟、高容量以及业务关键应用程序所需的可靠和安全无线网络。

专用移动网络可以实现如下方案：
- 命令和控制自动指导机动车 (仓库中的 AGVs) 。
- 智能工厂中机器人之间的实时通信。
- 扩充的现实和虚拟现实边缘应用程序。

 (vEPC) 网络功能的虚拟化演化数据包核心是专用移动网络的大脑。 你现在可以在专用边缘区域上部署 vEPC。 有关专用边缘区域提供的 vEPC 合作伙伴列表，请参阅 [VEPC isv](#vEPC)。

在专用边缘区域上部署专用移动网络解决方案需要其他组件，如移动访问点、SIM 卡和其他 VNFs （如路由器）。 若要设置专用移动网络，访问许可或未经许可的频谱是至关重要的。 而且，你可能需要有关射频规划、物理布局、安装和支持的帮助。 有关合作伙伴列表，请参阅 [移动广播合作伙伴](#mobile-radio)。

Microsoft 提供了一个合作伙伴生态系统，可以帮助解决此过程的所有方面。 合作伙伴可帮助规划网络、购买所需设备、设置硬件以及管理 Azure 中的配置。 与 Microsoft 紧密集成的一组经过验证的合作伙伴确保你的解决方案可靠且易于使用。 你可以专注于你的核心方案，并依赖 Microsoft 及其合作伙伴来帮助完成其余工作。

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-专用边缘区域上的 WAN

![SD-专用边缘区域上的 WAN](./media/edge-zones-overview/sd-wan.png "SD-专用边缘区域上的 WAN")

使用 SD，你可以创建企业级广域网， (Wan) 具有以下优势：

- 增加的带宽
- 对云的高性能访问
- 服务插入
- 可靠性
- 策略管理
- 广泛的网络可见性

SD-WAN 提供了无缝分支机构连接，该连接是通过冗余的中央控制器进行安排的，具有更低的拥有成本。
SD-专用边缘区域上的 WAN 允许从以 capex 为中心的模型移动到软件即服务 (SaaS) 模型，以降低 IT 预算。 你可以使用所选的 SD WAN 合作伙伴、orchestrator 或控制器启用新服务，并立即将其传播到整个网络。

## <a name="next-steps"></a>后续步骤

有关详细信息，请与以下团队联系：

* [边缘区域团队](https://aka.ms/EdgeZones)
* [专用边缘区域团队，成为合作伙伴](https://aka.ms/EdgeZonesPartner)
