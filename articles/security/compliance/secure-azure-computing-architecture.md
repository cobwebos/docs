---
title: 保护 Azure 计算体系结构
description: 此参考体系结构的企业级的外围网络体系结构使用网络虚拟设备和其他工具。 此体系结构旨在满足美国国防部的安全云计算体系结构功能要求。 此外可以使用任何组织。 此参考包括两个使用 Citrix 或 F5 设备的自动的选项。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963231"
---
# <a name="secure-azure-computing-architecture"></a>保护 Azure 计算体系结构

美国有关设置安全的虚拟网络和配置的安全工具和服务的规定的 DoD 标准和做法指南已要求将工作负荷部署到 Azure 的国防部 (DoD) 客户。 

防御信息系统局 (DISA) 发布[保护云计算体系结构 (SCCA) 功能要求文档 (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017年中。 SCCA 介绍有关保护防御信息系统网络的 (DISN) 和商业云提供者连接点的功能的目标。 SCCA 还介绍了如何安全的任务关键型所有者云连接边界处的应用程序。 连接到商业云的每个 DoD 实体必须遵循 SCCA FRD 中规定的准则。
 
SCCA 具有四个组件：
 
- 边界云访问点 (BCAP)
- 虚拟数据中心安全堆栈 (VDSS)
- 虚拟数据中心托管的服务 (VDM)
- 受信任云凭据管理器 (TCCM) 

Microsoft 已开发出满足在 Azure 中运行的 IL4 和 IL5 工作负荷的 SCCA 需求的解决方案。 此特定于 Azure 的解决方案是名为保护 Azure 计算体系结构 (SACA)。 符合 SCCA FRD 部署 SACA 的客户。 他们可以启用 DoD 客户能够将工作负荷转移到 Azure 后它们连接。

SCCA 指导和体系结构特定于 DoD 客户，但向 SACA 帮助负责客户的最新修订符合受信任的 internet 连接 （井字游戏） 指南。 最新版本还帮助商业客户想要实现安全外围网络以保护其 Azure 环境。


## <a name="secure-cloud-computing-architecture-components"></a>安全的云计算体系结构组件

### <a name="bcap"></a>BCAP

BCAP 旨在 DISN 防止在云环境中发起的攻击。 BCAP 执行入侵检测和防护。 它还筛选出未经授权的流量。 此组件可以与其他组件的 SCCA 共存。 我们建议使用物理硬件部署此组件。 下表中列出 BCAP 安全要求。

#### <a name="bcap-security-requirements"></a>BCAP 安全要求

![BCAP 要求矩阵](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS 旨在保护在 Azure 中托管的 DoD 所有者任务关键型应用程序。 VDSS SCCA 中执行大部分安全操作。 对通信检查来保护在 Azure 中运行的应用程序。 此组件可以在 Azure 环境中提供。

#### <a name="vdss-security-requirements"></a>VDSS 安全要求

![VDSS 要求矩阵](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDM 的目的是提供主机的安全性和共享数据中心服务。 VDM 的函数可以运行在你 SCCA 中心或任务关键型所有者可以部署部分应用在其自己特定的 Azure 订阅。 此组件可以在 Azure 环境中提供。

#### <a name="vdms-security-requirements"></a>VDM 安全要求

![VDM 要求矩阵](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM 是业务角色。 此类人员负责管理 SCCA。 其职责是： 

- 建立计划和策略的帐户对云环境访问权限。 
- 确保标识和访问管理运行正常。 
- 维护云凭据管理计划。 

此类人员任命的授权官员中。 BCAP、 VDSS 和的 VDM 提供 TCCM 执行其作业所需的功能。

#### <a name="tccm-security-requirements"></a>TCCM 安全要求

![TCCM 要求矩阵](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 组件和规划注意事项 

若要部署在 Azure 中的 VDSS 和 VDM 组件并启用 TCCM 设计 SACA 参考体系结构。 此体系结构是模块化。 所有条 VDSS 和 VDM 可以驻留在一个集中式中心中。 某些控件可以满足任务关键型所有者空间或甚至在本地。 Microsoft 建议您归置到中心的虚拟网络的所有任务关键型所有者可以通过连接到的 VDSS 和 VDM 组件。 下图显示了此体系结构： 


![SACA 参考体系结构关系图](media/sacav2generic.png)

当你计划 SCCA 合规性策略和技术体系结构时，请考虑从开始处的以下主题，因为它们会影响每个客户。 以下问题已经想出了很大的 DoD 客户，并往往会降低规划和执行。 

#### <a name="which-bcap-will-your-organization-use"></a>你的组织将使用哪个 BCAP？
   - DISA BCAP:
        - DISA 有两个操作 BCAPs 在五边形和训练营 Roberts、 CA。 第三个计划很快进入联机状态。 
        - DISA BCAPs 所有使用 Azure ExpressRoute 线路连接到 Azure，可用于通过 DoD 客户进行连接。 
        - DISA 的 DoD 客户想要为服务 (SaaS) 工具，例如 Office 365 订阅的 Microsoft 软件具有企业级 Microsoft 对等互连会话。 通过使用 DISA BCAP，可以启用连接和对等互连到 SACA 实例。 
    - 生成你自己 BCAP:
        - 此选项要求您租用归置的数据中心中的空间并将设置到 Azure 的 ExpressRoute 线路。 
        - 此选项需要其他批准。 
        - 由于其他批准和物理生成扩展，此选项占用最多时间。 
    - 我们建议使用 DISA BCAP。 此选项可立即、 具有内置冗余功能，并具有对其执行操作目前在生产环境中的客户。
- DoD 可路由 IP 空间：
    - 必须在边缘处使用 DoD 可路由 IP 空间。 若要使用 NAT 连接到 Azure 中的专用 IP 空间这些空格选项才可用。
    - 请联系 DoD 网络信息 Center (NIC)，以获取 IP 空间。 需要使用 DISA 系统/网络审批过程 （快照） 提交内容的一部分。 
    - 如果您计划使用 NAT 连接在 Azure 中的专用地址空间，你需要一个最少为/24 子网的地址空间分配从你打算部署 SACA 每个区域的 NIC。
- 冗余：
    - 将 SACA 实例部署到至少两个区域。 DoD 云中，你将其部署到这两个可用的 DoD 区域。
    - 连接到至少两个 BCAPs 通过单独的 ExpressRoute 线路。 然后，这两个 ExpressRoute 连接可以链接到每个区域的 SACA 实例中。 
- DoD 特定于组件的要求：
    - 你的组织是否具有 SCCA 要求之外的任何特定要求？ 某些组织具有特定 IP 要求。
- SACA 是模块化体系结构：
    - 使用仅为您的环境所需的组件。 
        - 部署网络虚拟设备在单层或多层中。
        - 使用集成的 IP 或将您自己的 IP。
- DoD 影响级别的应用程序和数据：
    - 如果可能会在 Microsoft IL5 区域中运行的应用程序，生成 IL5 对 SACA 实例。 前面 IL4 应用程序和 IL5，可以使用的实例。 IL4 SACA 实例的前面 IL5 应用程序最有可能不会收到鉴证。

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>为 VDSS 将使用哪个网络虚拟设备供应商？
前面曾提到，可以使用各种设备和 Azure 服务生成此 SACA 引用。 Microsoft 已自动部署使用 F5 和 Citrix SACA 体系结构的解决方案模板。 这些解决方案中的以下部分介绍。

#### <a name="which-azure-services-will-you-use"></a>你将使用哪些 Azure 服务？
- 有 Azure 服务可以满足要求的 log analytics、 基于主机的保护和 ID 的功能。 很可能某些服务在 Microsoft IL5 区域不是已正式发布。 在这种情况下，您可能需要使用第三方工具，如果这些 Azure 服务不能满足您的要求。 看看您应该已熟悉的工具和使用 Azure 的本机工具的可行性。
- 我们建议尽可能使用任意多个 Azure 本机工具。 它们使用云的安全考虑构建并与 Azure 平台的其余部分无缝集成。 以下列表中使用 Azure 的本机工具来满足各种 SCCA 要求：

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure 的第一道防线](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure 安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 大小调整
    - 必须完成大小调整操作。 看一下您可能通过 SACA 实例和网络吞吐量要求的并发连接数。 
    - 此步骤至关重要。 它有助于调整 Vm 的大小并确定所需来自 SACA 实例中使用不同供应商的许可证。 
    - 很好的成本分析才能完成此练习中调整大小。 为了获得最佳性能还允许正确的大小调整。 


## <a name="most-common-deployment-scenario"></a>最常见部署方案

 有多个 Microsoft 客户已通过完整的部署或在其 SACA 环境的最少的规划阶段。 亲身体验揭示深入了解的最常见的部署方案。 下图显示了最常见的体系结构： 


![SACA 参考体系结构关系图](media/sacav2commonscenario.png) 


您可以看到该关系图中，DoD 客户通常订阅两个 DISA BCAPs。 其中一种依赖西海岸和其他生活在东海岸。 在每个 DISA BCAP 位置启用了的 ExpressRoute 专用对等方。 这些 ExpressRoute 对等方然后链接到 DoD 东部和 DoD 中部 Azure 区域中的虚拟网络网关。 SACA 实例部署在 DoD 东部和 DoD 中部 Azure 区域中。 所有入口和出口流量将通过它与 DISA BCAP 的 ExpressRoute 连接。

任务所有者应用程序然后选择他们打算部署其应用程序的 Azure 区域。 它们使用对等互连来连接到 SACA 虚拟网络的应用程序的虚拟网络的虚拟网络。 然后它们强制隧道通过 VDSS 实例及其所有流量。

我们建议此体系结构，因为它符合 SCCA 要求。 它是高度可用且轻松地缩放。 它还简化了部署和管理。

## <a name="automated-saca-deployment-options"></a>自动的 SACA 部署选项

 如前所述，Microsoft 已经与两个供应商创建一个自动的 SACA 基础结构模板合作。 这两个模板部署以下 Azure 组件： 

- SACA 虚拟网络
    - 管理子网
        - 此子网是在其中部署管理 Vm 和服务，也称为跳转框。
        - VDM 子网
            - 此子网是部署 Vm 和服务使用的 VDM。
        - 不受信任和受信任的子网
            - 这些子网是虚拟设备的部署位置。
        - 网关子网
            - 此子网是部署 ExpressRoute 网关的位置。
- 管理跳转框虚拟机
    - 它们使用带外管理的环境。
- 网络虚拟设备
    - 使用任一 Citrix 或 F5 基于你所部署的模板。
- 公共 IP
    - ExpressRoute 联机之前，它们用于前端。 这些 Ip 将转换为后端 Azure 专用地址空间。
- 路由表 
    - 在自动化过程中应用，这些表强制隧道将所有流量都路由通过虚拟设备。
- Azure 负载均衡器的标准 SKU
    - 它们用于跨设备进行负载平衡流量。
- 网络安全组
    - 它们用于控制哪些类型的通信可以移到特定终结点。


### <a name="citrix-saca-deployment"></a>Citrix SACA 部署

Citrix 部署模板部署高度可用的 Citrix ADC 应用程序的两个层。 此体系结构以满足 VDSS。 

![Citrix SACA 关系图](media/citrixsaca.png)


有关 Citrix 文档和部署脚本中，请参阅[此 GitHub 链接](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)。


 ### <a name="f5-saca-deployment"></a>F5 SACA 部署

两个单独的 F5 部署模板包含两个不同的体系结构。 第一个模板具有主动-主动高度可用配置中的 F5 设备只有一个图层。 此体系结构满足 VDSS 的要求。 第二个模板都会添加一层的主动-主动高度可用 F5s 保障。 此第二个层，客户可以添加其自己独立于 F5 F5 层之间的 IP。 并非所有 DoD 组件都具有用于规定的特定 IP。 如果是这种情况，F5 设备的单个层适用于大多数中，因为该体系结构包括 F5 设备上的 IP。

![F5 SACA 关系图](media/f5saca.png)

F5 文档和部署脚本，请参阅[此 GitHub 链接](https://github.com/f5devcentral/f5-azure-saca)。












