---
title: 保护 Azure 计算体系结构
description: 这是一个企业级的外围网络体系结构，利用网络虚拟设备和其他工具的参考体系结构。 此体系结构旨在满足美国国防部的安全云计算体系结构功能要求。 但是，它可以为任何组织利用。 此引用包含两个使用 Citrix 或 F5 设备的自动的选项。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917591"
---
# <a name="secure-azure-computing-architecture"></a>保护 Azure 计算体系结构

快速越来越多的工作负荷部署到 Azure 的 DoD 客户要求的指南设置安全的虚拟网络和配置的安全工具和服务规定 DoD 标准和做法。 已发布的 DISA[保护云计算体系结构 (SCCA) 功能要求文档](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf)2017年中。 SCCA 描述了用于保护防御信息系统网络的 (DISN) 功能的目标和商业云提供程序连接点和如何关键任务连接边界处的所有者安全的云应用程序。 要求连接到商业云的每个 DoD 实体，如下所示 SCCA FRD 中规定的准则。
 
有四个组成部分 SCCA。 边界云访问点 (BCAP)、 虚拟数据中心安全堆栈 (VDSS)、 虚拟数据中心托管服务 (VDM) 和受信任的云凭据管理器 (TCCM)。 Microsoft 开发了一种解决方案，将满足 IL4 和 IL5 在 Azure 中运行的工作负荷的 SCCA 要求。 此 Azure 的特定解决方案称为安全 Azure 计算体系结构 (SACA)。 部署 SACA 的客户将符合 SCCA FRD，并且将使 DoD 客户能够将工作负荷转移到 Azure 一次连接。 

特定于 DoD 客户 SCCA 指导和体系结构时，负责客户符合受信任的 internet 连接 （井字游戏） 指南，以及想要实现安全外围的商业客户还将帮助 SACA 到最新版本保护其 azure 环境。


## <a name="secure-cloud-computing-architecture-components"></a>安全的云计算体系结构组件

**BCAP**

BCAP 旨在 DISN 防止在云环境中的攻击。 BCAP 将执行入侵检测和防护以及筛选出未经授权的流量。 此组件可以与其他组件的 SCCA 共存。 强烈建议使用物理硬件部署此组件。 您将在下面找到 BCAP 安全要求的列表。

***BCAP 安全要求***

![BCAP 要求矩阵](media/bcapreqs.png)


**VDSS**

VDSS 旨在保护 Azure 中托管的 DoD 任务关键型所有者应用程序。 VDSS SCCA 中执行大部分安全操作。 为了保护在 Azure 中运行的应用程序，它将执行的通信检查。 此组件可以在 Azure 环境中提供。

***VDSS 安全要求***

![VDSS 要求矩阵](media/vdssreqs.png)

**VDMS**

VDM 的目的是提供主机的安全性，以及共享数据中心服务。 VDM 的函数可以运行在你 SCCA 中心或任务关键型所有者可以部署部分应用在其自己特定的 Azure 订阅。 此组件可以在 Azure 环境中提供。

***VDM 安全要求***

![VDM 要求矩阵](media/vdmsreqs.png)


**TCCM**

TCCM 是业务角色。 此类人员将负责管理 SCCA。 其职责包括： 建立计划和策略的帐户访问云环境，确保标识和访问管理运行正常，以及维护云凭据管理计划。 此类人员任命的授权官方中。 BCAP、 VDSS 和的 VDM 将提供所需的 TCCM 来执行其作业功能的功能。

***TCCM 安全要求***

![TCCM 要求矩阵](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 组件和规划注意事项 

用于部署在 azure 中，VDSS 和 VDM 组件，以及启用 TCCM SACA 参考体系结构。 此体系结构是模块化，这意味着所有条 VDSS 和 VDM 可以驻留在一个集中式中心中或某些控件可以满足任务关键型所有者空间或甚至在本地。 我们 Microsoft 团队的建议是归置到中心虚拟网络的所有任务关键型所有者可以通过连接到的 VDSS 和 VDM 组件。 下图描绘了我们的建议体系结构。 


![SACA 参考体系结构关系图](media/sacav2generic.png)

在规划 SCCA 合规性策略和技术体系结构时，有许多事情需要考虑。 它很重要，以下主题也纳入考虑范围从一开始，因为每个客户将需要介绍这些。 下面的主题已与真正的 DoD 客户已经提出和往往会变慢的规划和执行的问题。 

- 你的组织将使用哪个 BCAP？
    - DISA BCAP
        - DISA 都具有两个操作 BCAPs 五边形，以及这一观点 Roberts CA，在第三个即将推出。 
        - DISA BCAPs 所有使用 ExpressRoute 线路连接到 Azure，便可供连接的 DoD 客户。 
        - DISA 已拥有要订阅 Office 365 等 Microsoft SaaS 工具的 DoD 客户的企业级 Microsoft 对等互连会话。 通过使用 DISA BCAP，可以启用连接和对等互连到 SACA 实例。 
    - 构建您自己 BCAP
        - 这需要你来租用归置的数据中心中的空间和设置到 Azure 的 ExpressRoute 线路。 
        - 此选项都需要其他批准 
        - 由于获得额外的批准和物理构建，此选项占用最多时间。 
    - Microsoft 的建议是利用 DISA BCAP。 此选项可立即、 具有内置的冗余，并已在其上目前在生产环境中运行的客户。
- DoD 可路由 IP 空间
    - 你将需要在边缘处使用 DoD 可路由 IP 空间。 选项以通过 nat 转换到 Azure 中的专用 IP 空间才可用。  
    - 请联系 DoD NIC 以获取 IP 空间，它将需要使用 DISA 你管理单元的提交内容的一部分。 
    - 如果打算以通过 nat 转换到 Azure 中的专用地址空间，您将需要最少/24 子网的地址空间分配从你打算部署 SACA 每个区域的 NIC。 
- 冗余 
    - Microsoft 建议将 SACA 实例部署到至少两个区域中。 DoD 云中，这就意味着将其部署到这两个可用的 DoD 区域。 
    - 另外，建议你连接到至少两个 BCAPs 通过单独的 ExpressRoute 线路。 然后，这两个 Express 路由可以链接到每个区域的 SACA 实例中。 
- DoD 特定于组件的要求
    - 你的组织是否具有 SCCA 要求之外的任何特定要求？ （某些组织具有特定 IP 要求）
- SACA 是模块化体系结构  
    - 使用您的环境需要仅哪些组件。 
        - 单层或多层中部署 Nva
        - 集成的 IP，或引入自己的 IP
- DoD 影响级别的应用程序和数据
    - 如果没有发生在我们 IL5 区域中运行的应用程序的可能性，建议生成 IL5 对 SACA 实例。 前面 IL4 应用程序，以及 IL5，可以使用的实例。 但是，IL4 SACA 实例的前面 IL5 应用程序将很可能不会收到鉴证。 
- 为 VDSS 将使用哪个网络虚拟设备供应商？
    - 前面曾提到，可以使用各种设备和 Azure 服务生成此 SACA 引用。 但是，我们确实有自动化的解决方案模板部署使用 F5 和 Citrix SACA 体系结构。 将下面更详细地介绍这些解决方案。 
- 你将使用哪些 Azure 服务？
    - 有可以满足要求 log analytics、 基于主机的保护和 ID 功能的 Azure 服务。 但是，有可能某些服务 IL5 区域不是已正式发布。 这可能会导致需要使用某些第三方工具，如果这些 Azure 服务不能满足你的要求。 您需要查看哪些工具可是熟悉与使用本机 Azure 工具的可行性。 
    - 它是 Microsoft 的建议使用尽可能使用云的安全考虑而构建并与 Azure 平台的其余部分无缝集成的 Azure 多本机工具。 下面是 Azure 的本机工具可以利用以满足各种要求的 SCCA 的列表。 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview) 
        - [Azure 的第一道防线](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 调整大小
    - 大小调整操作将需要完成。 您需要看一下您可能通过 SACA 实例，以及网络吞吐量要求的并发连接数。 
    - 这是一个关键步骤，因为它将帮助以 Vm 的大小，以及有助于确定将需要来自 SACA 实例中，你将使用不同供应商的许可证。 
    - 不能完成很好的成本分析而无需此大小调整活动，它还有一点需要确保所有内容具有正确的大小以便获得最佳性能。 


## <a name="most-common-deployment-scenario"></a>最常见部署方案

Microsoft 已具有已经历了完整的多个客户部署或至少规划其 SACA 环境的阶段。 这样，若要深入了解的最常见的部署方案。 下图描绘了最常见的体系结构。 


![SACA 参考体系结构关系图](media/sacav2commonscenario.png) 


您可以看到该关系图中，DoD 客户通常订阅两个 DISA BCAPs，这些业务西海岸依赖于和其他的生活东海岸之一。 在每个 DISA BCAP 位置启用了的 ExpressRoute 专用对等方。 这些 ExpressRoute 对等方然后链接到 DoD 东部和 DoD 中部的 Azure 区域中的虚拟网络网关。 SACA 实例部署在 DoD 东部和 DoD 中部 Azure 区域和所有入口和出口流量通过它来回流动于 DISA BCAP 的 Express Route 连接。 

关键任务应用程序然后选择哪些 Azure 区域，它们将部署在其应用程序的所有者和使用虚拟网络对等互连来连接他们的应用程序的虚拟网络到 SACA 虚拟网络。 强制隧道通过 VDSS 实例及其所有流量。 

Microsoft 强烈建议此体系结构，因为它将满足 SCCA 要求，它是高度可用、 易于扩展，它简化了部署和管理。

## <a name="automated-saca-deployment-options"></a>自动的 SACA 部署选项

 前面我们提到过 Microsoft 已经与两个供应商创建一个自动的 SACA 基础结构模板合作。 这两个模板将部署以下 Azure 组件。 

- SACA 虚拟网络
    - 管理子网
        - 管理 Vm 和服务的部署位置 （跳转盒）
        - VDM 子网
            - 部署 Vm 和服务使用的 VDM
        - 不受信任和受信任的子网 
            - 其中部署虚拟设备
        - 网关子网
            - 将在其中部署 ExpressRoute 网关
- 管理跳转框虚拟机
    - 用于带外管理的环境。
- 网络虚拟设备
    - Citrix 或 F5，具体取决于哪些模板进行部署。
- 公共 IP
    - ExpressRoute 联机之前，用于前端。 这些 Ip 会转换到后端 Azure 专用地址空间
- 路由表 
    - 在过程中应用自动化，这些路由表强制隧道的所有流量通过虚拟设备
- Azure 负载均衡器的标准 SKU
    - 使用这些流量进行负载平衡跨设备
- 网络安全组
    - 用于控制哪些类型的通信可以遍历到特定终结点


**Citrix SACA 部署**

Citrix 已创建部署高度可用的 Citrix ADC 应用程序的两个层的部署模板。 此体系结构以满足 VDSS。 

![Citrix SACA 关系图](media/citrixsaca.png)


Citrix 文档和部署脚本可以找到[此处。](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **F5 SACA 部署**

F5 已创建涵盖两个不同的体系结构的两个单独的部署模板。 第一个具有主动-主动高度可用配置中的 F5 设备只有一个图层。 此体系结构满足 VDSS 的要求。 第二个添加第二个层的主动-主动高度可用 F5s。 此第二个图层的目的是允许客户将添加其自己独立于 F5 F5 层之间的 IP。 并非所有 DoD 组件都具有用于规定的特定 IP。 如果是这种情况，F5 设备的单个层将适用于最以来体系结构，包括 F5 设备上的 IP。  

![Citrix SACA 关系图](media/f5saca.png)

F5 文档和部署脚本可以找到[此处。](https://github.com/f5devcentral/f5-azure-saca) 












