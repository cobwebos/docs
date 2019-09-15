---
title: 保护 Azure 计算体系结构
description: 企业级 DMZ 体系结构的此参考体系结构使用网络虚拟设备和其他工具。 此体系结构旨在满足防御部门的安全云计算体系结构功能要求。 它还可用于任何组织。 此参考包括两个使用 Citrix 或 F5 设备的自动选项。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001873"
---
# <a name="secure-azure-computing-architecture"></a>保护 Azure 计算体系结构

美国将工作负荷部署到 Azure 的美国国防部（DoD）客户已要求提供设置安全虚拟网络的指导，并配置由 DoD 标准和实践规定的安全工具和服务。 

防御信息系统代理（DISA）已在2017中发布[安全的云计算体系结构（SCCA）功能要求文档（FRD）](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) 。 SCCA 介绍保护防御信息系统网络（DISN）和商业云提供商连接点的功能目标。 SCCA 还介绍了任务所有者如何保护连接边界的云应用程序。 连接到商业云的每个 DoD 实体都必须遵循 SCCA FRD 中所述的指导原则。
 
SCCA 有四个组件：
 
- 边界云访问点（BCAP）
- 虚拟数据中心安全堆栈（VDSS）
- 虚拟数据中心托管服务（VDMS）
- 受信任的云凭据管理器（TCCM） 

Microsoft 开发了一个解决方案，该解决方案可满足在 Azure 中运行的 IL4 和 IL5 工作负载的 SCCA 要求。 此特定于 Azure 的解决方案称为安全 Azure 计算体系结构（SACA）。 部署 SACA 的客户符合 SCCA FRD。 它们可让 DoD 客户在连接后将工作负荷转移到 Azure。

SCCA 指南和体系结构特定于 DoD 客户，但 SACA 的最新修订版本有助于民用客户遵守可信 internet 连接（票）指导。 最新的修订还有助于要实现安全外围网络的商业客户保护其 Azure 环境。


## <a name="secure-cloud-computing-architecture-components"></a>保护云计算体系结构组件

### <a name="bcap"></a>BCAP

BCAP 的目的是保护 DISN 免受来自云环境的攻击。 BCAP 执行入侵检测和防护。 它还筛选出未经授权的流量。 此组件可以与 SCCA 的其他组件并存。 建议使用物理硬件部署此组件。 下表列出了 BCAP 的安全要求。

#### <a name="bcap-security-requirements"></a>BCAP 安全要求

![BCAP 要求矩阵](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS 的目的是保护托管在 Azure 中的 DoD 任务所有者应用程序。 VDSS 在 SCCA 中执行大部分安全操作。 它将执行流量检查来保护在 Azure 中运行的应用程序。 可以在 Azure 环境中提供此组件。

#### <a name="vdss-security-requirements"></a>VDSS 安全要求

![VDSS 要求矩阵](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDMS 的目的是提供主机安全和共享数据中心服务。 VDMS 的功能可在 SCCA 的中心运行，或任务所有者可以将其部分部署在其自己的特定 Azure 订阅中。 可以在 Azure 环境中提供此组件。

#### <a name="vdms-security-requirements"></a>VDMS 安全要求

![VDMS 要求矩阵](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM 是一种业务角色。 此人员负责管理 SCCA。 其职责为： 

- 建立用于帐户访问云环境的计划和策略。 
- 确保标识和访问管理正常运行。 
- 维护云凭据管理计划。 

此人由授权官方来进行。 BCAP、VDSS 和 VDMS 提供 TCCM 执行其工作所需的功能。

#### <a name="tccm-security-requirements"></a>TCCM 安全要求

![TCCM 要求矩阵](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 组件和规划注意事项 

SACA 参考体系结构设计用于在 Azure 中部署 VDSS 和 VDMS 组件并启用 TCCM。 此体系结构是模块化的。 所有 VDSS 和 VDMS 部分都可以在集中式中心内运行。 某些控件可以在任务所有者的空间中或甚至是在本地。 Microsoft 建议将 VDSS 和 VDMS 组件共同定位到一个中央虚拟网络，所有任务所有者都可以通过该网络进行连接。 下图显示了此体系结构： 


![SACA 参考体系结构示意图](media/sacav2generic.png)

在规划 SCCA 合规性策略和技术体系结构时，请考虑开始以下主题，因为它们会影响每个客户。 以下问题已与 DoD 客户一起使用，并且往往会降低规划和执行的速度。 

#### <a name="which-bcap-will-your-organization-use"></a>你的组织将使用哪些 BCAP？
   - DISA BCAP：
        - DISA 有两个可在五个 BCAPs 的操作，在 Camp Roberts，CA。 第三个计划稍后会联机。 
        - DISA 的 BCAPs 都将 Azure ExpressRoute 线路连接到 Azure，DoD 客户可以使用它来进行连接。 
        - 对于想要订阅 Microsoft 软件即服务（SaaS）工具（如 Office 365）的 DoD 客户，DISA 有企业级 Microsoft 对等互连会话。 通过使用 DISA BCAP，可以启用与 SACA 实例的连接和对等互连。 
    - 构建你自己的 BCAP：
        - 此选项要求您在归置的数据中心租赁空间，并将 ExpressRoute 线路设置为 Azure。 
        - 此选项需要额外批准。 
        - 由于额外的批准和物理生成，此选项会占用最多时间。 
    - 建议使用 DISA BCAP。 此选项随时可用，具有内置冗余，并且客户现在可以在生产环境中对其进行操作。
- DoD 可路由 IP 空间：
    - 你必须在边缘使用 DoD 可路由 IP 空间。 使用 NAT 将这些空间连接到 Azure 中的专用 IP 空间的选项可用。
    - 请联系 DoD 网络信息中心（NIC）获取 IP 空间。 你需要它作为你的系统/网络批准过程（快照）通过 DISA 提交的一部分。 
    - 如果你计划使用 NAT 连接 Azure 中的专用地址空间，则需要为你计划在其中部署 SACA 的每个区域的 NIC 中至少分配一个/24 子网地址空间。
- 冗余：
    - 将 SACA 实例至少部署到两个区域。 在 DoD 云中，你可以将其部署到两个可用的 DoD 区域。
    - 通过单独的 ExpressRoute 线路连接到至少两个 BCAPs。 然后，可以将 ExpressRoute 连接链接到每个区域的 SACA 实例。 
- DoD 组件特定要求：
    - 你的组织是否有任何特定要求超出 SCCA 要求？ 某些组织具有特定的 IPS 要求。
- SACA 是模块化体系结构：
    - 仅使用你的环境所需的组件。 
        - 在单层或多层部署网络虚拟设备。
        - 使用集成的 IP 或自带的 IP。
- 应用程序和数据的 DoD 影响级别：
    - 如果任何可能在 Microsoft IL5 区域中运行的应用程序，请在 IL5 中生成 SACA 实例。 实例可在 IL4 应用程序和 IL5 之前使用。 IL5 应用程序前面的 IL4 SACA 实例最有可能不会获得资格鉴定。

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>你将使用哪种网络虚拟设备供应商进行 VDSS？
如前文所述，你可以使用各种设备和 Azure 服务构建此 SACA 参考。 Microsoft 提供了自动解决方案模板，可同时用 F5 和 Citrix 部署 SACA 体系结构。 以下部分介绍了这些解决方案。

#### <a name="which-azure-services-will-you-use"></a>将使用哪些 Azure 服务？
- 有一些 Azure 服务可以满足 log analytics、基于主机的保护和 IDS 功能的要求。 某些服务可能并不是在 Microsoft IL5 区域中公开提供的。 在这种情况下，如果这些 Azure 服务无法满足你的要求，你可能需要使用第三方工具。 查看你熟悉的工具以及使用 Azure 本机工具的可行性。
- 建议尽可能多地使用 Azure 本机工具。 它们是基于云安全而构建的，并与 Azure 平台的其余部分无缝集成。 使用以下列表中的 Azure 本机工具来满足 SCCA 的各种要求：

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure 前门](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure 安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS 防护](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 大小调整
    - 必须完成调整大小的练习。 查看可能通过 SACA 实例和网络吞吐量要求的并发连接数。 
    - 此步骤非常重要。 它有助于调整 Vm 大小，并识别在 SACA 实例中使用的各种供应商所需的许可证。 
    - 如果没有此调整操作，则不能进行良好的成本分析。 正确调整大小还能获得最佳性能。 


## <a name="most-common-deployment-scenario"></a>最常见的部署方案

 几个 Microsoft 客户已经完成了整个部署，或者至少已完成了其 SACA 环境的规划阶段。 他们的经验揭示了最常见的部署方案。 下图显示了最常见的体系结构： 


![SACA 参考体系结构示意图](media/sacav2commonscenario.png) 


从关系图中可以看出，DoD 客户通常订阅了两个 DISA BCAPs。 其中一个是 West Coast，另一个位于东海岸。 在每个 DISA BCAP 位置，向 Azure 启用 ExpressRoute 专用对等互连。 然后，将这些 ExpressRoute 对等互连到 DoD 东部和 DoD 中央 Azure 区域中的虚拟网络网关。 SACA 实例部署在 DoD 东部和 DoD 中央 Azure 区域中。 所有入站和出站流量都流过它并从 ExpressRoute 连接传递到 DISA BCAP。

然后，任务所有者应用程序选择他们计划在其中部署应用程序的 Azure 区域。 它们使用虚拟网络对等互连将其应用程序的虚拟网络连接到 SACA 虚拟网络。 然后，它们会强制通过 VDSS 实例对其所有流量进行隧道传输。

建议采用此体系结构，因为它符合 SCCA 要求。 它高度可用且可轻松缩放。 它还简化了部署和管理。

## <a name="automated-saca-deployment-options"></a>自动 SACA 部署选项

 如前文所述，Microsoft 已与两个供应商合作创建一个自动 SACA 基础结构模板。 这两个模板都部署以下 Azure 组件： 

- SACA 虚拟网络
    - 管理子网
        - 此子网用于部署管理 Vm 和服务，也称为跳转盒。
        - VDMS 子网
            - 此子网用于部署用于 VDMS 的 Vm 和服务。
        - 不受信任和受信任的子网
            - 其中部署了虚拟设备的子网。
        - 网关子网
            - 此子网是 ExpressRoute 网关的部署位置。
- 管理跳转框虚拟机
    - 它们用于对环境进行带外管理。
- 网络虚拟设备
    - 你可以根据部署的模板使用 Citrix 或 F5。
- 公共 IP
    - 它们用于前端，直到 ExpressRoute 进入联机状态。 这些 Ip 会转换为后端 Azure 专用地址空间。
- 路由表 
    - 在自动化期间应用，这些路由表通过虚拟设备强制隧道所有流量。
- Azure 负载均衡器-标准 SKU
    - 它们用于对跨设备的流量进行负载均衡。
- 网络安全组
    - 它们用于控制哪些类型的流量可以遍历到特定终结点。


### <a name="citrix-saca-deployment"></a>Citrix SACA 部署

Citrix 部署模板部署高可用 Citrix ADC 设备的两个层。 此体系结构满足 VDSS 的要求。 

![Citrix SACA 关系图](media/citrixsaca.png)


有关 Citrix 文档和部署脚本，请参阅[此 GitHub 链接](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)。


 ### <a name="f5-saca-deployment"></a>F5 SACA 部署

两个单独的 F5 部署模板涵盖了两个不同的体系结构。 第一个模板在主动-主动高可用配置中只有一个 F5 设备层。 此体系结构满足 VDSS 的要求。 第二个模板添加了另一层主动-主动高可用性 F5s。 此第二层允许客户在 F5 层之间添加自己的 IP，使其独立于 F5。 并非所有 DoD 组件都有规定使用的特定 IP。 如果是这种情况，则 F5 装置的单个层最适用于大多数，因为该体系结构包括 F5 设备上的 IP。

![F5 SACA 关系图](media/f5saca.png)

对于 F5 文档和部署脚本，请参阅[此 GitHub 链接](https://github.com/f5devcentral/f5-azure-saca)。












