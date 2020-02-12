---
title: Azure 开发测试实验室的企业参考体系结构
description: 本文提供了企业中 Azure 开发测试实验室的参考体系结构指导。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132849"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>适用于企业的 Azure 开发测试实验室参考体系结构
本文提供了参考体系结构，可帮助你根据企业中的 Azure 开发测试实验室部署解决方案。 其中包括以下内容：
- 通过 Azure ExpressRoute 进行的本地连接
- 远程桌面网关，用于远程登录到虚拟机
- 到专用项目的项目存储库的连接
- 实验室中使用的其他 PaaS 服务

![参考体系结构示意图](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>体系结构
以下是参考体系结构的关键要素：

- **Azure Active Directory （Azure AD）** ：开发测试实验室使用[用于标识管理的 Azure AD 服务](../active-directory/fundamentals/active-directory-whatis.md)。 当你为用户授予基于开发测试实验室的环境的访问权限时，请考虑以下两个重要方面：
    - **资源管理**：它提供对 Azure 门户的访问，以管理资源（创建虚拟机、创建环境、启动、停止、重启、删除和应用项目等）。 资源管理是使用基于角色的访问控制（RBAC）在 Azure 中完成的。 你将角色分配给用户，并设置资源和访问级别权限。
    - **虚拟机（网络级）** ：在默认配置中，虚拟机使用本地管理员帐户。 如果有可用的域（[Azure AD 域服务](../active-directory-domain-services/overview.md)、本地域或基于云的域），则计算机可以加入域。 然后，用户可以使用基于域的标识来连接到 Vm。
- **本地连接**：在体系结构关系图中，使用[ExpressRoute](../expressroute/expressroute-introduction.md) 。 但也可以使用[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 尽管开发测试实验室不需要 ExpressRoute，但它通常用于企业。 仅当需要访问企业资源时，才需要 ExpressRoute。 常见方案包括：
    - 你有不能移到云中的本地数据。
    - 你更喜欢将实验室的虚拟机加入本地域。
    - 你想要通过本地防火墙强制执行所有网络流量，以实现安全/合规性。
- **网络安全组**：根据源和目标 IP 地址将流量限制到云环境（或云环境内）的常用方法是使用[网络安全组](../virtual-network/security-overview.md)。 例如，你希望只允许来自企业网络的流量进入实验室网络。
- **远程桌面网关**：企业通常会阻止企业防火墙上的传出远程桌面连接。 有几个选项可用于实现与开发测试实验室中基于云的环境的连接，其中包括：
  - 使用[远程桌面网关](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，并允许使用网关负载均衡器的静态 IP 地址。
  - 通过 ExpressRoute/站点到站点 VPN 连接[定向所有传入的 RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 当企业规划开发测试实验室部署时，此功能是一个常见的考虑因素。
- **网络服务（虚拟网络、子网）** ： [Azure 网络](../networking/networking-overview.md)拓扑是开发测试实验室体系结构中的另一个关键元素。 它控制实验室中的资源是否可进行通信，并可访问本地和 internet。 体系结构关系图包含客户使用开发测试实验室的最常见方式：通过使用[中心辐射型模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)连接到本地的 ExpressRoute/站点到站点 VPN 连接，所有实验室通过[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)进行连接。 但开发测试实验室直接使用 Azure 虚拟网络，因此，不会对设置网络基础结构的方式有任何限制。
- **开发测试实验室**：开发测试实验室是整个体系结构的关键部分。 若要了解有关该服务的详细信息，请参阅[关于开发测试实验室](devtest-lab-overview.md)。
- **虚拟机和其他资源（SaaS、PaaS、IaaS）** ：虚拟机是一项关键工作负载，开发测试实验室支持与其他 Azure 资源一起使用。 借助开发测试实验室，企业能够轻松快速地提供对 Azure 资源（包括 Vm 和其他 Azure 资源）的访问权限。 详细了解如何访问[面向开发人员](devtest-lab-developer-lab.md)和[测试](devtest-lab-test-env.md)人员的 Azure。

## <a name="scalability-considerations"></a>可伸缩性注意事项
尽管开发测试实验室没有内置配额或限制，但在实验室典型操作中使用的其他 Azure 资源具有[订阅级配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。 因此，在典型的企业部署中，需要多个 Azure 订阅才能涵盖大型部署的开发测试实验室。 企业最常访问的配额包括：

- **资源组**：在默认配置中，开发测试实验室为每个新虚拟机创建一个资源组，或使用该服务创建一个环境。 订阅[最多可包含980个资源组](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 因此，这是一个订阅中的虚拟机和环境限制。 还应考虑另外两种配置：
    - **[所有虚拟机都将转向同一资源组](resource-group-control.md)** ：尽管此设置可帮助你满足资源组限制，但会影响资源-每个资源组的限制。
    - **使用共享公共 ip**：相同大小和区域的所有 vm 都进入同一资源组。 如果允许虚拟机具有公共 IP 地址，则在资源组配额与资源类型的资源组配额之间，此配置是 "中间"。
- 每个资源组的资源数每个资源**类型**：每个资源组的资源默认限制为[800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)。  当你使用 "*所有 vm" 访问相同的资源组*配置时，用户可以更快地达到此订阅限制，尤其是在 vm 有多个额外磁盘的情况下。
- **存储帐户**：开发测试实验室中的实验室带有存储帐户。 每个[订阅每个区域的存储帐户数](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)的 Azure 配额为250。 同一区域中的开发测试实验室的最大数目也是250。
- **角色分配**：角色分配是指如何向用户或主体授予资源（所有者、资源、权限级别）的访问权限。 在 Azure 中，[每个订阅的角色分配数限制为 2000](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits)。 默认情况下，开发测试实验室服务为每个 VM 创建一个资源组。 所有者授予了对资源组的开发测试实验室 VM 和*读取*者权限的*所有者*权限。 这样，你创建的每个新 VM 将使用两个角色分配，以及向用户授予对实验室的权限时使用的分配。
- **API 读取/写入**：可通过多种方式自动执行 Azure 和开发测试实验室，包括 REST Api、PowerShell、Azure CLI 和 Azure SDK。 通过自动化，你可能会遇到 API 请求的另一个限制：每个订阅最多允许[12000 个读取请求，每小时1200个写入请求](../azure-resource-manager/management/request-limits-and-throttling.md)。 当你自动化开发测试实验室时，请注意此限制。

## <a name="manageability-considerations"></a>可管理性注意事项
开发测试实验室提供了一个很好的管理用户界面来使用单个实验室。 但在企业中，您可能有多个 Azure 订阅和许多实验室。 将更改统一到所有实验室需要脚本编写/自动化。 下面是开发测试实验室部署的一些示例和最佳管理实践：

- 对**实验室设置的更改**：一种常见方案是在部署中的所有实验室更新特定实验室设置。 例如，新的 VM 实例大小可用，必须更新所有实验室以允许它。 最好是使用 PowerShell 脚本、CLI 或 REST Api 自动执行这些更改。  
- **项目存储库个人访问令牌**：通常，Git 存储库的个人访问令牌将在90天、一年或两年后过期。 若要确保连续性，必须扩展个人访问令牌或创建一个新令牌。 然后，使用自动化将新的个人访问令牌应用于所有实验室。
- **限制对实验室设置的更改**：通常必须限制特定设置（例如允许使用 marketplace 映像）。 可以使用 Azure 策略来防止更改资源类型。 也可以创建自定义角色，并向用户授予该角色（而不是*实验室角色）的角色。* 你可以对实验室中的大多数设置执行此操作（内部支持、实验室公告、允许的 VM 大小，等等）。
- **需要 vm 遵循命名约定**：管理人员通常希望轻松地识别成为基于云的开发和测试环境的一部分的 vm。 可以使用[Azure 策略](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)执行此操作。

请务必注意，开发测试实验室使用相同方式管理的底层 Azure 资源：网络、磁盘、计算等。 例如，Azure 策略适用于在实验室中创建的虚拟机。 Azure 安全中心可以报告 VM 符合性。 Azure 备份服务可为实验室中的 Vm 提供定期备份。

## <a name="security-considerations"></a>安全注意事项
Azure 开发测试实验室使用 Azure 中的现有资源（计算、网络等）。 因此，它会自动受益于平台内置的安全功能。 例如，要只从公司网络中请求传入的远程桌面连接，只需将网络安全组添加到远程桌面网关上的虚拟网络即可。 唯一的另一个安全注意事项是您向团队成员授予日常使用实验室的权限级别。 最常见的权限是 " [*所有者*" 和 "*用户*](devtest-lab-add-devtest-user.md)"。 有关这些角色的详细信息，请参阅[在 Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>后续步骤
请参阅本系列文章中的下一篇文章：[向上缩放 Azure 开发测试实验室基础结构](devtest-lab-guidance-scale.md)。
