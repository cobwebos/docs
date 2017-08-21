---
title: "Microsoft Azure 虚拟数据中心 | Microsoft Docs"
description: "了解如何在 Azure 中生成虚拟数据中心"
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jonor
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: fd656c68b5c3b6858b0aa04c51bdd28f3f0adc24
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---

# <a name="microsoft-azure-virtual-data-center"></a>Microsoft Azure 虚拟数据中心
**Microsoft Azure**：更快更省地迁移、集成本地应用和数据

## <a name="overview"></a>概述
将本地应用程序迁移到 Azure（即使是不做任何重大更改的“直接迁移”）可为组织提供安全而具成本效益的基础结构，使组织从中受益。 但是，若要充分利用云计算的灵活性，企业应提升体系结构，充分利用 Azure 的功能。 Microsoft Azure 提供超大规模的服务和基础结构、企业级的功能和可靠性以及许多混合连接选项。 客户可以选择通过 Internet 或 Azure ExpressRoute（提供专用网络连接）访问这些云服务。 Microsoft Azure 平台可让客户无缝地将基础结构扩展到云中并构建多层体系结构。 此外，Microsoft 合作伙伴还可以通过提供在 Azure 中运行的优化安全服务和虚拟设备实现增强型功能。

许多客户在考虑将全部内容迁移到云时都会担心结构规模、性能和安全问题，本文概述了可用于解决这些问题的模式和设计。 本文还概述了如何使不同的 IT 组织角色适应系统的管理和监管，同时强调了安全要求和成本优化。

## <a name="what-is-a-virtual-data-center"></a>什么是虚拟数据中心？
早期的云解决方案旨在托管相对独立的单个应用程序，主要涉及公共范畴。 此方法在刚开始的几年富有成效。 然而，随着云解决方案的优势日益明显并且云上托管的大规模工作负荷越来越多，解决在一个或多个区域进行部署的安全性、可靠性、性能和成本问题成为重中之重，这项任务贯穿了云服务的整个生命周期。

以下云部署关系图展示了关于跨工作负荷优化虚拟网络设备的安全隐患（红盒子）和空间（黄盒子）的一些示例。

[![0]][0]

支持企业工作负荷要求能够进行缩放，并且需要处理在公有云中支持大规模应用程序时遇到的问题，正因这两点，虚拟数据中心 (vDC) 应运而生。

vDC 不仅只是云中的应用程序工作负荷，也是网络、安全、管理和基础结构（如 DNS 和目录服务）。 它通常还提供可返回本地网络或数据中心的专用连接。 随着越来越多的工作负荷迁移到 Azure，必须考虑放置这些工作负荷的支持基础结构和对象。 认真考虑如何结构化资源可以避免激增成百上千的“工作负荷集群”（这种工作负荷集群必须通过独立数据流、安全模型和符合性质询单独管理）。

虚拟数据中心本质上是一系列独立但相关的实体集合，这些实体拥有共同的支持函数、功能和基础结构。 将工作负荷视作集成型 vDC 可以实现组件和数据流的集中化，获得规模效益和更优的安全性，降低成本，同时简化操作、管理和符合性审核。

> [!NOTE]
> 必须认识到 vDC 不是一款离散的 Azure 产品，而是能够满足确切需求的多项特性和功能的组合。 考虑工作负荷的托管时，条件反射的是 vDC，Azure 使用它在云中实现资源和能力的最大化。 因此，虚拟 DC 是尊重组织角色和责任在 Azure 中建立 IT 服务的模块化方法。

在以下情形下，vDC 可帮助企业将工作负荷和应用程序迁移到 Azure：

-   托管多个相关工作负荷
-   将工作负荷从本地环境迁移到 Azure
-   跨工作负荷实现共享化或中心化的安全性和访问需求
-   为大型企业适当混合 DevOps 和中心化 IT

解锁 vDC 优势的关键在于中心化拓扑（中心辐射型）和多项 Azure 功能的混合：[Azure VNet][VNet]、[NSG][NSG]、[VNet 对等互连][VNetPeering]、[用户定义的路由 (UDR)][UDR] 以及具有[基于角色的访问控制 (RBAC)][RBAC] 的 Azure 标识。

## <a name="who-needs-a-virtual-data-center"></a>谁需要虚拟数据中心？
需要将多个工作负荷迁移到 Azure 的任何 Azure 客户都可以考虑使用公共资源并从中受益。 根据数量级，即使单个应用程序也可从使用用于生成 vDC 的模式和组件受益。

如果组织具备中心化 IT、网络、安全性和/或符合性团队/部门，vDC 可以帮助实施策略点和责任分离，在基于需求给予应用程序团队尽可能多的自由和控制权的同时确保基础公共组件的一致性。

期待 DevOps 的组织可以利用 vDC 概念提供 Azure 资源的授权包，同时确保他们在相应的组（公共订阅中的订阅组或资源组）内拥有完全控制权，但网络和安全边界必须按照中心 VNet 和资源组内中心化策略的定义保持合规。

## <a name="considerations-on-implementing-a-virtual-data-center"></a>实现虚拟数据中心的注意事项
设计 vDC 时，需考虑以下几项关键问题：

-   标识和目录服务
-   安全基础结构
-   与云的连接
-   云内的连接

##### <a name="identity-and-directory-service"></a>*标识和目录服务*
标识和目录服务是所有数据中心（本地数据中心和云数据中心）的关键方面。 标识与 vDC 内服务的访问和授权的各个方面相关。 为了帮助确保只有获得授权的用户和进程可以访问 Azure 帐户和资源，Azure 使用几种凭据进行身份验证。 这些凭据包括密码（用于访问 Azure 帐户）、加密密钥、数字签名和安全证书。 [*Azure 多重身份验证 (MFA)*][MFA] 是访问 Azure 服务的附加安全层。 Azure MFA 通过一系列简单的验证选项（电话、短信或移动应用通知）提供强身份验证，用户可根据自己的偏好选择所用的方法。

任何大型企业都需要定义标识管理流程，说明如何在 vDC 内部或跨 vDC 管理各个标识、其身份验证、授权、角色和权限。 其目标是在减少成本、停机时间和重复人工作业的同时提高安全性和工作效率。

企业/组织可以要求严格混合针对不同业务线 (LOB) 的服务，员工在参与不同的项目时通常会有不同的角色。 vDC 要求不同的团队（每个团队拥有特定的角色定义）精诚合作，确保系统运行并很好地监管。 责任、访问和权限矩阵可能十分复杂。 vDC 中的标识管理通过 [*Azure Active Directory* (AAD)][AAD] 和基于角色的访问控制 (RBAC) 实现。

目录服务是用于查找、控制、管理和组织日常项目和网络资源的共享信息基础结构。 这些资源包括卷、文件夹、文件、打印机、用户、组、设备和其他对象。 目录服务器将网络上的每个资源视作一个对象。 资源信息作为与该资源或对象相关的属性集合存储。

所有 Microsoft Online 业务服务都依赖于 Azure Active Directory (AAD) 进行登录并满足其他标识需求。 Azure Active Directory 是一个综合性的、高度可用的标识和访问管理云解决方案，它将核心目录服务、高级标识监管和应用程序访问管理结合起来。 AAD 可与本地 Active Directory 集成，为基于云的和本地托管的（本地）所有应用程序启用单一登录。 本地 Active Directory 的用户属性可以自动同步到 AAD。

在 vDC 中分配所有权限不需要单个全局管理员。 而是每个特定部门（或目录服务中的用户组或服务组）拥有在 vDC 内管理其资源所需的权限。 权限的结构化需要均衡。 权限过多会妨碍性能效率，权限过少或过松则会增加安全风险。 Azure 基于角色的访问控制 (RBAC) 通过对 vDC 资源提供细致的访问管理帮助解决此问题。

##### <a name="security-infrastructure"></a>*安全基础结构*
在 vDC 环境中，安全基础结构主要与 vDC 特定虚拟网络段中的流量分离以及如何控制整个 vDC 的入口流量和出口流量相关。 Azure 基于多租户的基础结构，该基础结构通过使用虚拟网络 (VNet) 隔离、访问控制列表 (ACL)、负载均衡器、IP 筛选器和流量流策略防止部署间的未经授权流量和意外流量。 网络地址转换 (NAT) 将内部网络流量与外部流量分开。

Azure 结构将基础结构资源分配到租户工作负荷，并管理与虚拟机 (VM) 的通信。 Azure 虚拟机监控程序会在 VM 之间强制实施内存和进程隔离，并将网络流量安全路由到来宾 OS 租户。

##### <a name="connectivity-to-the-cloud"></a>*与云的连接*
vDC 需要与外部网络连接才能为客户、合作伙伴和/或内部用户提供服务。 这通常意味着不仅要连接到 Internet，还要连接到本地网络和数据中心。

客户可以生成安全策略，使用网络虚拟设备（提供筛选和流量检查）、自定义路由策略和网络筛选（用户定义的路由和网络安全组）控制特定的 vDC 托管服务与 Internet 的通信内容和方式。

企业通常需要将 vDC 连接到本地数据中心或其他资源。 因此，Azure 与本地网络的连接是设计高效体系结构的关键方面。 企业可以通过两种不同方式在 Azure 内实现 vDC 与本地资源的互连：通过 Internet 和/或专用直接连接传输。

[**Azure 站点到站点 VPN**][VPN] 是通过 Internet 使本地网络与 vDC 实现互连的服务，该互连服务通过安全加密连接（IPsec/IKE 隧道）建立。 由于所有连接都通过 Internet 实现，因此 Azure 站点到站点的连接创建起来灵活而快速，不需要任何进一步的采购。

[**ExpressRoute**][ExR] 是可在 vDC 与本地网络之间建立专用连接的 Azure 连接服务。 ExpressRoute 连接不通过公共 Internet，从而提供更高的安全性、可靠性、更快的速度（最高可达 10 Gbps）和一致延迟。 由于 ExpressRoute 客户可以从与专用连接相关的符合性规则受益，因此 ExpressRoute 对 vDC 十分有用。

部署 ExpressRoute 连接涉及与 ExpressRoute 服务提供程序通信。 对于需要快速入门的客户，通常先使用站点到站点 VPN 在 vDC 与本地资源之间建立连接，然后再迁移到 ExpressRoute 连接。

##### <a name="connectivity-within-the-cloud"></a>*云内的连接*
[VNet][VNet] 和 [VNet 对等互连][VNetPeering]是 vDC 中的基本网络连接服务。 VNet 保证 vDC 资源具有自然隔离边界，VNet 对等互连允许同一 Azure 区域内的不同 VNet 相互通信。 VNet 内部以及 VNet 之间的流量控制需要与通过访问控制列表（[网络安全组][NSG]）、[网络虚拟设备][NVA]和自定义路由表 ([UDR][UDR]) 指定的一组安全规则匹配。

## <a name="virtual-data-center-overview"></a>虚拟数据中心概述

### <a name="topology"></a>拓扑
中心辐射模型在单个 Azure 区域内扩展了虚拟数据中心

[![1]][1]

中心是控制和检查不同区域（Internet、本地和辐射）之间入口和/或出口流量的中心区域。 中心辐射型拓扑为 IT 部门提供了在中心位置强制实施安全策略的有效方式，同时降低了配置错误和曝光的可能性。

中心包含辐射使用的常见服务组件。 以下是几个常见中心服务的典型示例：

-   第三方从不受信任网络访问时，Windows Active Directory 基础结构（具有相关 ADFS 服务）要求进行用户身份验证，验证通过后才可访问辐射中的工作负荷
-   DNS 服务用于解析辐射中工作负荷的命名，也用于访问本地和 Internet 上的资源
-   PKI 基础结构用于实现工作负荷的单一登录
-   辐射与 Internet 之间的流控制 (TCP/UDP)
-   辐射与本地之间的流控制
-   一个辐射与另一个辐射之间的流控制（如果需要）

vDC 通过在多个辐射之间使用共享中心基础结构来降低总体成本。

每个辐射的角色可以是托管不同类型的工作负荷。 辐射也可以为同一工作负荷的可重复部署（如开发和测试、用户验收测试、预生产和生产）提供模块化方法。 辐射也可以用于隔离和启用组织内部的不同组（如 DevOps 组）。 在辐射内，可以通过各层之间的流量控制部署基本工作负荷或复杂的多层工作负荷。

##### <a name="subscription-limits-and-multiple-hubs"></a>订阅限制和多个中心
在 Azure 中，每个组件（无论何种类型）都通过 Azure 订阅进行部署。 不同 Azure 订阅中的 Azure 组件的隔离可满足不同 LOB 的需求，如设置不同级别的访问和授权。

尽管与每个 IT 系统一样具有平台限制，单个 vDC 仍能增加到大量辐射。 中心部署与特定的 Azure 订阅绑定，该订阅具有相关的约束和限制（如 VNet 对等互连上限 - 请参阅 [Azure 订阅和服务限制、配额和约束条件][Limits]获取详细信息）。 如果出现限制问题，体系结构可以通过将模型从单个中心辐射型扩展为中心辐射群集实现进一步增加。 位于一个或多个 Azure 区域的多个中心可以使用 ExpressRoute 或站点到站点 VPN 实现互连。

[![2]][2]

引入多个中心会增加系统的成本和管理工作量，引入的唯一原因在于可伸缩性（如系统限制或冗余）和区域复制（如最终用户性能或灾难恢复）。 在要求多个中心的情形下，所有中心应力图提供相同的服务集以方便操作。

##### <a name="interconnection-between-spokes"></a>辐射之间的互连
在单个辐射内，可以实现复杂的多层工作负荷。 在同一 VNet 内使用子网（每层使用一个子网）并使用 NSG 对流进行筛选可以实现多层配置。

另一方面，架构师可能希望跨多个 VNet 部署多层工作负荷。 使用 VNet 对等互连，辐射可以连接到同一中心的其他辐射，也可连接到其他中心的其他辐射。 此情形的典型示例是：应用程序处理服务器位于一个辐射 (VNet) 内，而数据库部署在另一个辐射 (VNet) 中。 在这种情况下，可以轻松实现辐射与 VNet 对等互连之间的互连，从而避免通过中心传输。 应仔细审查体系结构和安全性，确保绕过中心并不会绕过可能只存在于中心的重要安全或审核点。

[![3]][3]

辐射也可以与充当中心的辐射互连。 此方法可以创建一个两层的层次结构：在该层次结构中，较高级别（第 0 层）的辐射成为较低级别（第 1 层）辐射的中心。 vDC 的辐射需要将流量转发到中心，以与本地网络或 Internet 连接。 具有两层中心的体系结构引入了复杂路由，该路由会消除简单中心辐射关系的优势。

尽管 Azure 允许复杂拓扑，但 vDC 概念的核心原则之一是可重复性和简洁性。 为了尽量减少管理工作量，推荐使用简单的中心辐射设计作为 vDC 参考体系结构。

### <a name="components"></a>组件
虚拟数据中心由四个基本组件类型构成：基础结构、外围网络、工作负荷和监视。

每个组件类型由各种 Azure 功能和资源组成。 vDC 由多个组件类型的实例和同一组件类型的多个变体构成。 例如，可能有许多逻辑上分离的不同工作负荷实例表示不同应用程序。 使用这些不同的组件类型和实例最终生成 vDC。

[![4]][4]

上图中的高级别 vDC 体系结构显示了中心辐射型拓扑的不同区域使用的不同组件类型。 此关系图显示了该体系结构中各个部分的基础结构组件。

访问权限和特权应该基于组，这是针对本地 DC 或 vDC 的良好做法。 处理组而非单个用户有助于跨团队一致地维护访问策略，尽可能减少配置错误。 在相应的组中分配或删除用户有助于使特定用户的权限保持最新。

每个角色组的名称应具有唯一的前缀，以便轻松确定哪个组与哪个工作负荷关联。 例如，托管身份验证服务的工作负荷应具有名为 AuthServiceNetOps、AuthServiceSecOps、AuthServiceDevOps 和 AuthServiceInfraOps 的组。 同样，中心化角色或不与特定服务相关的角色的名称可以用“Corp”开头，如 CorpNetOps。

许多组织都使用以下组的变体来提供主要角色细分：

-   中心 IT 组 (Corp) 拥有控制基础结构（如网络和安全性）组件的所有权，因此需要在订阅上拥有参与者角色（拥有中心的控制权），并且在辐射中拥有网络参与者角色。 大型组织时常会在多个团队（如专门处理网络的网络操作 (CorpNetOps) 组和负责防火墙和安全策略的安全操作 (CorpSecOps) 组）之间划分这些管理责任。 在这种特定情况下，需要创建两个不同的组，用于分配这些自定义角色。
-   开发与测试 (AppDevOps) 组负责部署工作负荷（应用或服务）。 此组担任用于 IaaS 部署的虚拟机参与者角色和/或一个或多个 PaaS 参与者角色（请参阅[用于 Azure 基于角色的访问控制的内置角色][Roles]）。 （可选）开发与测试团队可能需要对中心或特定辐射内的安全策略 (NSG) 和路由策略 (UDR) 拥有可见性。 因此，除了担任工作负荷参与者角色，此组也需要担任网络读取者角色。
-   操作和维护组（CorpInfraOps 或 AppInfraOps）负责管理生产中的工作负荷。 此组必须是任何生产订阅中工作负荷的订阅参与者。 一些组织还可能评估他们是否需要在生产和中心订阅中担任订阅参与者角色的附加升级支持团队组，以便修复生产环境中的潜在配置问题。

vDC 是结构化的，可确保为中心 IT 组创建的用于管理中心的组在工作负荷级别拥有相应的组。 除了管理中心资源之外，只有中心 IT 组能够在订阅中控制外部访问和最高权限。 但是，工作负荷组将能够在中心 IT 内独立地控制其 VNet 中的资源和权限。

需要对 vDC 进行分区处理，以便安全托管跨不同业务线 (LOB) 的多个项目。 所有项目需要不同的独立环境（如开发、UAT 和生产）。 每个环境的各个 Azure 订阅自然隔离。

[![5]][5]

上方关系图显示组织中项目、用户、组以及部署 Azure 组件的环境之间的关系。

在 IT 中，环境（或层）通常是部署和执行多个应用程序的系统。 大型企业使用开发环境（最初做出更改和测试的环境）和生产环境（最终用户使用的环境）。 这些环境相互隔离，环境之间通常存在多个过渡环境，出现问题时可实现分阶段部署（推出）、测试和回退。 部署体系结构之间存在巨大差异，但通常遵循以下基本流程：始于开发 (DEV)，终于生产 (PROD)。

此类多层环境的常见体系结构包括 DevOps（发展与测试）、UAT（过渡）和生产环境。 组织可以利用单个或多个 Azure AD 租户定义对这些环境的访问和权限。 上方关系图显示使用两个不同 Azure AD 租户的事例：一个用于 DevOps 和 UAT，另一个专门用于生产。

不同的 Azure AD 租户可以强化环境之间的隔离。 同一组用户（如中心 IT）需要使用不同 URI 进行身份验证，从而访问不同的 AD 租户，该租户可修改项目的 DevOps 或生产环境的角色或权限。 通过不同的用户身份验证访问不同的环境可以减少人为失误可能导致的中断和其他问题。

#### <a name="component-type-infrastructure"></a>组件类型：基础结构
大多数支持基础结构都位于此组件类型中。 中心化 IT、安全团队和/或符合性团队的大部分时间花费在该类型上。

[![6]][6]

基础结构组件为 vDC 的不同组件提供互连，并同时位于中心和辐射中。 管理和维护基础结构组件的责任通常分配给中心 IT 和/或安全团队。

IT 基础结构团队的首要任务之一是保证整个企业 IP 地址架构的一致性。 分配给 vDC 的专用 IP 地址空间需要一致，并且不能与本地网络上分配的专用 IP 地址重叠。

NAT 在本地边缘路由器或 Azure 环境中时可避免 IP 地址冲突，但会增加基础结构组件的复杂性。 简化管理是 vDC 的关键目标之一，因此不建议使用 NAT 处理 IP 问题。

基础结构组件包含以下功能：

-   [**标识和目录服务**][AAD]。 对 Azure 中的每种资源类型的访问都由目录服务中存储的标识控制。 目录服务不仅存储用户列表，也存储特定 Azure 订阅中资源的访问权限。 这些服务可只存在于云中，也可以与 Active Directory 中存储的本地标识同步。
-   [**虚拟网络**][VPN]。 虚拟网络是 vDC 的主要组件之一，通过虚拟网络可以在 Azure 平台创建流量隔离边界。 虚拟网络由单个或多个虚拟网络段构成，每个虚拟网络段都有一个特定的 IP 网络前缀（子网）。 虚拟网络定义内部外围区域，在该区域中 IaaS 虚拟机和 PaaS 服务可以建立专用通信。 一个虚拟网络中的 VM（和 PaaS 服务）不能与另一个虚拟网络中的 VM（和 PaaS 服务）直接通信，即使这两个虚拟网络是由同一个客户在同一订阅下创建的。 隔离是一个非常关键的属性，可确保客户 VM 与通信在虚拟网络中保持私密性。
-   [**UDR**][UDR]。 虚拟网络中的流量默认基于系统路由表路由。 用户定义的路由是自定义路由表，网络管理员可以将该路由表关联到一个或多个子网，覆盖系统路由表的行为并定义虚拟网络中的通信路径。 UDR 保证辐射的出口流量通过中心和辐射中的特定自定义 VM 和/或虚拟网络设备以及负载均衡器传输。
-   [**NSG**][NSG]。 网络安全组是一系列安全规则，充当 IP 源、IP 目标、协议、IP 源端口和 IP 目标端口的流量筛选器。 NSG 可应用于子网和/或与 Azure VM 关联的虚拟 NIC 卡。 NSG 对在中心和辐射中实现正确的流控制至关重要。 NSG 提供的安全级别由出于何种原因打开哪个端口决定。 客户应通过基于主机的防火墙（如 IPtable 或 Windows 防火墙）对每个 VM 应用附加筛选器。
-   **DNS**。 vDC 中 VNet 资源的名称解析通过 DNS 提供。 默认 DNS 的名称解析范围受限于 VNet。 通常情况下，自定义 DNS 服务需要作为常见服务的一部分在中心部署，但 DNS 服务的主要使用者位于辐射中。 如有必要，客户可以通过向辐射委派 DNS 区域创建分层的 DNS 结构。
-   [**订阅][SubMgmt]和[资源组管理][RGMgmt]**。 订阅定义在 Azure 创建多个资源组的自然边界。 订阅中的资源聚集在名为资源组的逻辑容器中。 资源组表示组织 vDC 资源的逻辑组。
-   [**RBAC**][RBAC]。 通过 RBAC，可以映射组织角色以及访问特定 Azure 资源的权限，允许限制用户只进行特定子网操作。 使用 RBAC，可以通过将相应的角色分配给相关范围内的用户、组和应用程序授予访问权限。 角色分配的范围可以是 Azure 订阅、资源组或单个资源。 RBAC 允许继承权限。 分配在父范围内的角色也会将访问权限授予给其中所含的子范围。 使用 RBAC，可以对职责进行分配，仅向用户授予执行作业所需的访问权限。 例如，使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL DB。
-   [**VNet 对等互连**][VNetPeering]。 用于创建 vDC 基础结构的基本功能是 VNet 对等互连，它是通过 Azure 数据中心网络连接同一区域内两个虚拟机 (VNet) 的机制。

#### <a name="component-type-perimeter-networks"></a>组件类型：外围网络
[外围网络][DMZ]组件（又称为 DMZ 网络）允许与本地或实际数据中心网络建立连接，并允许与 Internet 建立连接。 它也是网络和安全团队花费大部分时间的地方。

传入数据包应该在流经中心的安全设备（例如防火墙、IDS 和 IPS）之后才到达辐射中的后端服务器。 来自工作负荷的 Internet 绑定数据包也应流经外围网络中的安全设备，经过策略实施、检查和审核之后，才离开网络。

外围网络组件提供以下功能：

-   [虚拟网络][VNet]、[UDR][UDR] 和 [NSG][NSG]
-   [网络虚拟设备][NVA]
-   [负载均衡器][ALB]
-   [应用程序网关][AppGW] / [WAF][WAF]
-   [公共 IP][PIP]

通常情况下，中心 IT 和安全团队负责外围网络的需求定义和操作。

[![7]][7]

上方关系图显示通过访问 Internet 和本地网络加强两个外围，这两个外围都位于中心。 在单个中心内，可以增加连接到 Internet 的外围网络，通过使用多个 Web 应用程序防火墙 (WAF) 和/或防火墙场支持大量的 LOB。

[**虚拟网络**][VNet] 通常在 VNet 上生成中心，提供多个子网托管不同类型的服务，并通过 NVA、WAF 和 Azure 应用程序网关筛选和检查与 Internet 的通信流量。

[**UDR**][UDR] 客户可以使用 UDR 部署防火墙、IDS/IPS 和其他虚拟设备，并通过这些安全设备路由网络流量，以实施安全边界策略、审核和检查。 可以同时在中心和辐射中创建 UDR，保证流量通过 vDC 使用的特定自定义 VM、网络虚拟设备和负载均衡器传输。 若要保证由辐射中的 VM 生成的流量传输到正确的虚拟设备，需要通过将内部负载均衡器的前端 IP 地址设置为下一个跃点在辐射的子网中设置 UDR。 内部负载均衡器将内部流量分配到虚拟设备（负载均衡器后端池）。

[![8]][8]

[**网络虚拟设备**][NVA] 在中心内，能访问 Internet 的外围网络通常通过防火墙场和/或 Web 应用程序防火墙 (WAF) 进行管理。

不同的 LOB 通常使用许多 Web 应用程序，这些应用程序易遭受各种漏洞和潜在攻击。 Web 应用程序防火墙是一款特殊产品，相较通用防火墙而言，它能够更深入的检测对 Web 应用程序 (HTTP/HTTPS) 的攻击。 与传统防火墙技术相比，WAF 拥有一组用于保护内部 Web 服务器免受威胁的特定功能。

防火墙场是一组在同一公共管理下串联工作的防火墙，拥有一组安全规则，用于保护辐射中托管的工作负荷并控制对本地网络的访问权限。 与 WAF 相比，防火墙场的专用软件更少，但应用范围广泛，可以筛选和检查出口和入口的任何流量类型。 防火墙场通常通过网络虚拟设备 (NVA) 在 Azure 中实现，Azure Marketplace 提供了这些设备。

建议对源自 Internet 的流量使用一组 NVA，对源自本地的流量使用另一组 NVA。 若仅对两组网络流量使用一组 NVA，则存在安全风险，因为它不会在两组网络流量之间提供安全外围。 使用不同 NVA 可以降低检查安全规则的复杂性，并指明哪项规则与哪项传入网络请求对应。

大多数大型企业都管理多个域。 Azure DNS 可以用来托管某个特定域的 DNS 记录。 例如，可以在 Azure DNS 记录的 A 记录中登记 Azure 外部负载均衡器（或 WAF）的虚拟 IP 地址 (VIP)。

[**Azure 负载均衡器**][ALB] Azure 负载均衡器提供高度可用的第 4 层（TCP 和 UDP）服务，可以在负载均衡集定义的服务实例间分配传入流量。 可以将由前端终结点（公共 IP 终结点或专用 IP 终结点）发送到负载均衡器的流量重新分配（通过/不通过地址转换）到一组后端 IP 地址池（如网络虚拟设备或 VM）。

Azure 负载均衡器也可以探测各种服务器实例的运行状态，当探测无法响应时，负载均衡器会停止向不正常的实例发送流量。 在 vDC 中，中心的外部负载均衡器可以均衡发送到 NVA 的流量，辐射中的外部负载均衡器可以执行均衡多层应用程序的不同 VM 之间的流量等任务。

[**应用程序网关**][AppGW] Microsoft Azure 应用程序网关是一个专用的虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，为应用程序提供各种第 7 层负载均衡功能。 可以用它将 CPU 密集型 SSL 终点卸载到应用程序网关，优化 Web 场工作效率。 它还提供其他第 7 层路由功能，包括传入流量的轮循机制分配、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后面托管多个网站的能力。 Web 应用程序防火墙 (WAF) 也作为 WAF SKU 应用程序网关的一部分提供。 此 SKU 可保护 Web 应用程序免受 Web 常见漏洞和攻击的影响。 可以将应用程序网关配置为面向 Internet 的网关、仅内部网关或这两者的组合。 

[**公共 IP**][PIP] 可以通过一些 Azure 功能将服务终结点关联到允许从 Internet 访问资源的公共 IP 地址。 该终结点使用网络地址转换 (NAT) 将流量路由到 Azure 虚拟网络上的内部地址和端口。 此路径是外部流量进入虚拟网络的主要方式。 可以对公共 IP 地址进行配置，确定可以传入哪种流量、如何在虚拟网络上转换该流量以及要将它路由到何处。

#### <a name="component-type-monitoring"></a>组件类型：监视
监视组件可以监视所有其他组件类型并报警。 所有团队都应有权监视他们有权访问的组件和服务。 如果拥有中心化支持人员或操作团队，他们需要对这些组件提供的数据拥有集成访问权限。

Azure 提供不同类型的记录和监视服务，跟踪 Azure 托管资源的行为。 对 Azure 中工作负荷的管理和控制不仅只基于收集日志数据，也基于根据特定报告事件触发操作的能力。

Azure 中主要有两种日志类型：

-   [**活动日志**][ActLog]（也称为“运行日志”）提供相关信息，方便用户了解对 Azure 订阅中的资源执行的操作。 这些日志报告订阅的控制平面事件。 每个 Azure 资源都会生成审核日志。

-   [**Azure 诊断日志**][DiagLog]是资源生成的日志，记录与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。

[![9]][9]

在 vDC 中，跟踪 NSG 日志极其重要，尤其是以下信息：

-   [**事件日志**][NSGLog]：提供根据 MAC 地址向 VM 和实例角色应用的 NSG 规则的信息。
-   [**计数器日志**][NSGLog]：跟踪每个 NSG 规则拒绝或允许流量的执行次数。

所有日志都可以存储在 Azure 存储帐户中，以便进行审核、静态分析或备份。 日志存储在 Azure 存储帐户时，客户可以对该数据使用不同类型的框架进行检索、准备、分析和可视化操作，以报告云资源的状态和运行状况。

大型企业应该已经获取到用于监视本地系统的标准框架，并可以扩展该框架以集成云部署生成的日志。 对于希望将所有日志记录存储在云中的组织，[Microsoft Operations Management Suite (OMS)][OMS] 是一个不错的选择。 由于 OMS 作为基于云的服务实现，因此在基础结构服务上进行极低的投资即可快速使其启动并运行。 OMS 还可与 System Center Operations Manager 等 System Center 组件集成，将现有管理投资扩展到云。

OMS Log Analytics 是 OMS 框架的组件，用于帮助收集、关联、搜索以及处理由操作系统、应用程序和基础结构云组件生成的日志和性能数据。 使用集成搜索和自定义仪表板为客户提供实时操作见解，分析 vDC 中所有工作负荷的所有记录。

#### <a name="component-type-workloads"></a>组件类型：工作负荷
工作负荷组件是实际应用程序和服务所在的位置。 它也是应用程序开发团队花费大部分时间的地方。

工作负荷的可能性是无限的。 以下是几种可能的工作负荷类型：

**内部 LOB 应用程序**

业务线应用程序是对企业的日常运营十分重要的计算机应用程序。 LOB 应用程序具有以下常见特征：

-   **交互性**。 LOB 应用程序本身具有交互性：输入数据，然后返回结果/报告。
-   **数据驱动**。 LOB 应用程序是数据密集型应用程序，频繁访问数据库或其他存储。
-   **集成性**。 LOB 应用程序可与组织内外的其他系统集成。

**面向客户的 Web 站点（面向 Internet 或内部）**与 Internet 交互的大多数应用程序是 Web 站点。 Azure 提供在 IaaS VM 上或从 [Azure Web 应用][WebApps]站点 (PaaS) 运行网站的功能。 Azure Web 应用支持与允许在 vDC 的辐射中部署 Web 应用的 VNet 集成。 通过 VNet 集成，便不需要向应用程序公开 Internet 终结点，但可以改为从专用 VNet 使用非 Internet 的资源专用可路由地址。

**大数据/分析** 当数据需要增加到极大的量时，数据库可能无法正常增加。 Hadoop 技术提供可在大量节点上并行运行所分配查询的系统。 客户可以选择在 IaaS VM 或 PaaS ([HDInsight][HDI]) 中运行数据工作负荷。 HDInsight 支持部署到基于位置的 VNet，可以部署到 vDC 辐射中的群集。

**事件和消息传输**
[Azure 事件中心][EventHubs]是超大规模的遥测引入服务，可收集、传输和存储数以百万的事件。 作为分布式流式处理平台，它提供低延迟和可配置的保留时间，使用户可以将大量遥测数据引入到 Azure 中，并从多个应用程序中读取数据。 通过事件中心，单个流可以同时支持实时管道和基于批处理的管道。

应用程序与服务之间的高度可靠云消息传递服务可以通过 [Azure 服务总线][ServiceBus]实现，该服务总线提供客户端和服务器之间的异步中转消息传送、结构化先进先出 (FIFO) 消息传送和发布/订阅能力。

[![10]][10]

### <a name="multiple-vdc"></a>多个 vDC
到目前为止，本文围绕单个 vDC 介绍了构成可复原 vDC 的基本组件和体系结构。 Azure 功能（如 Azure 负载均衡器、NVA、可用性集、规模集和其他机制）构成一个允许在生产服务中生成稳定 SLA 级别的系统。

然而，单个 vDC 托管在单个区域中，易受到可能影响整个区域的重大故障的影响。 希望实现高 SLA 级别的客户需要通过在位于不同区域的两个（或更多）vDC 中部署相同的项目来保护服务。

除了 SLA 问题之外，以下几种常见情形也可以部署多个 vDC：

-   区域/全球影响力
-   灾难恢复
-   在 DC 之间转移流量的机制

#### <a name="regionalglobal-presence"></a>区域/全球影响力
Azure 数据中心遍布世界各地。 选择多个 Azure 数据中心时，客户需要考虑两个相关因素：地理距离和延迟。 客户需要评估 vDC 之间的地理距离以及 vDC 和最终用户之间的距离，从而获得最佳用户体验。

托管 vDC 的 Azure 区域也需要符合由所在组织所属的任何法律管辖区域制定的法规要求。

#### <a name="disaster-recovery"></a>灾难恢复
灾难恢复计划的实现与所涉及的工作负荷类型以及同步不同 vDC 之间工作负荷状态的能力紧密相关。 理想情况下，大多数客户希望同步在两个不同 vDC 中运行的部署之间的应用程序数据，从而快速实现故障转移机制。 大多数应用程序易受延迟影响，这可能在数据同步时导致潜在超时和延迟。

对不同 vDC 中应用程序的同步性和检测信号的监视需要在不同 vDC 之间建立通信。 可以通过以下方式连接不同区域中的两个 vDC：

-   当 vDC 中心连接到同一 ExpressRoute 线路时使用 ExpressRoute 专用对等互连
-   通过企业主干和连接到 ExpressRoute 线路的 vDC 对等网格连接多个 ExpressRoute 线路
-   在每个 Azure 区域中的 vDC 中心建立站点到站点 VPN 连接

通常情况下，ExpressRoute 连接是首选机制，因为通过 Microsoft 主干传输时具有更高的带宽和一致性延迟。

没有魔法秘诀来验证在不同区域中的两个（或多个）不同 vDC 之间分配的应用程序。 客户应通过运行网络资格测试来验证连接的延迟和带宽，检查同步或异步数据复制是否合适，以及确定工作负荷的最佳恢复时间目标 (RTO)。

#### <a name="mechanism-to-divert-traffic-between-dc"></a>在 DC 之间转移流量的机制
将传入一个 DC 的流量转移到另一个 DC 的有效方法是基于 DNS。 [Azure 流量管理器][TM]使用域名系统 (DNS) 机制将最终用户的流量转移到特定 vDC 中最合适的公共终结点。 通过探测，流量管理器可以定期检查不同 vDC 中公共终结点的服务运行状况，如果这些终结点出现故障，流量管理器将自动路由到辅助 vDC。

流量管理器适用于 Azure 公共终结点，可以使用它将流量控制/转移到合适的 vDC 中的 Azure VM 和 Web 应用。 流量管理器具有复原能力，即使整个 Azure 区域出现故障，它仍可以基于多个标准（例如，特定 vDC 中出现服务故障，或选择对客户端拥有最低网络延迟的 vDC）控制不同 vDC 中服务终结点的用户流量分配。

### <a name="conclusion"></a>结束语
虚拟数据中心是使用一系列功能和能力将数据中心迁移到云的方法，在 Azure 中创建可缩放的体系结构，最大程度地使用云资源、降低成本和简化系统管理。 vDC 概念基于中心辐射型拓扑，在中心提供常见的共享服务并允许辐射中存在特定的应用程序/工作负荷。 vDC 与企业角色的结构匹配，在该结构中，不同部门（中心 IT、DevOps、操作和维护部门）共同合作，每个部门拥有特定的角色和责任。 vDC 满足“直接迁移”的需求，也为本机云部署带来众多优势。

## <a name="references"></a>参考
本文档中讨论了以下功能。 单击链接以了解更多信息。

| | | |
|-|-|-|
|网络功能|负载均衡|连接|
|[Azure 虚拟网络][VNet]</br>[网络安全组][NSG]</br>[NSG 日志][NSGLog]</br>[用户定义的路由][UDR]</br>[网络虚拟设备][NVA]</br>[公共 IP 地址][PIP]|[Azure 负载均衡器 (L3)][ALB]</br>[应用程序网关 (L7)][AppGW]</br>[Web 应用程序防火墙][WAF]</br>[Azure 流量管理器][TM] |[VNet 对等互连][VNetPeering]</br>[虚拟专用网络][VPN]</br>[ExpressRoute][ExR]
|标识</br>|监视</br>|最佳实践</br>|
|[Azure Active Directory][AAD]</br>[多重身份验证][MFA]</br>[基于角色的访问控制][RBAC]</br>[默认 AAD 角色][Roles] |[活动日志][ActLog]</br>[诊断日志][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br> |[外围网络最佳实践][DMZ]</br>[订阅管理][SubMgmt]</br>[资源组管理][RGMgmt]</br>[Azure 订阅限制][Limits] |
|其他 Azure 服务|
|[Azure Web 应用][WebApps]</br>[HDInsights (Hadoop)][HDI]</br>[事件中心][EventHubs]</br>[服务总线][ServiceBus]|



## <a name="next-steps"></a>后续步骤
 - 探索 [VNet 对等互连][VNetPeering]，这是 vDC 中心辐射型设计的基础技术
 - 实现 [AAD][AAD]，开始探索 [RBAC][RBAC]
 - 开发符合所在组织的结构、需求和策略的订阅和资源管理模型和 RBAC 模型。 最重要的活动是计划。 针对重组、合并和新产品线等，计划应尽可能实用。

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "组件重叠示例" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "中心辐射型 vDC 的高级别示例"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "中心辐射型群集"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "辐射到辐射"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "vDC 的块级关系图"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "用户、组、订阅和项目"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "高级别基础结构关系图"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "高级别基础结构关系图"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "VNet 对等互连和外围网络"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "用于监视的高级别关系图"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "用于工作负荷的高级别关系图"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg 
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[WebApps]: https://docs.microsoft.com/azure/app-service-web/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview

