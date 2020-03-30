---
title: Azure 开发人员测试实验室的企业参考体系结构
description: 本文为企业中的 Azure 开发人员测试实验室提供了参考体系结构指南。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132849"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>面向企业的 Azure 开发人员测试实验室参考体系结构
本文提供了参考体系结构，以帮助您在企业中部署基于 Azure 开发人员测试实验室的解决方案。 它包括以下内容：
- 通过 Azure 快速路由进行本地连接
- 远程桌面网关，可远程登录到虚拟机
- 连接到私有项目的项目存储库
- 实验室中使用的其他 PaaS 服务

![参考体系结构图](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>体系结构
这些是参考体系结构的关键元素：

- **Azure 活动目录 （Azure AD）：** 开发人员测试实验室使用[Azure AD 服务进行标识管理](../active-directory/fundamentals/active-directory-whatis.md)。 在授予用户基于 DevTest Labs 的环境访问权限时，请考虑以下两个关键方面：
    - **资源管理**：它提供对 Azure 门户的访问来管理资源（创建虚拟机;创建环境;启动、停止、重新启动、删除和应用项目;等等）。 使用基于角色的访问控制 （RBAC） 在 Azure 中执行资源管理。 将角色分配给用户并设置资源和访问级别权限。
    - **虚拟机（网络级）：** 在默认配置中，虚拟机使用本地管理员帐户。 如果存在可用的域[（Azure AD 域服务](../active-directory-domain-services/overview.md)、本地域或基于云的域），则可以将计算机加入域。 然后，用户可以使用其基于域的标识连接到 VM。
- **本地连接**：在我们的体系结构图中，使用[ExpressRoute。](../expressroute/expressroute-introduction.md) 但是您也可以使用[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 尽管 DevTest 实验室不需要 ExpressRoute，但它在企业中很常见。 仅当您需要访问公司资源时，才需要 ExpressRoute。 常见方案包括：
    - 您有无法移动到云的本地数据。
    - 您希望将实验室的虚拟机加入本地域。
    - 您希望通过本地防火墙强制所有网络流量进出云环境，以确保安全/合规性。
- **网络安全组**：根据源和目标 IP 地址限制流量到云环境（或云环境中）的一种常见方法是使用[网络安全组](../virtual-network/security-overview.md)。 例如，您希望仅允许来自公司网络的流量进入实验室的网络。
- **远程桌面网关**：企业通常会在公司防火墙上阻止传出远程桌面连接。 在 DevTest 实验室中，有几个选项可用于实现与基于云的环境的连接，包括：
  - 使用[远程桌面网关](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，并允许网关负载均衡器的静态 IP 地址。
  - 通过 ExpressRoute/站点到站点 VPN 连接[引导所有传入的 RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 当企业规划 DevTest Labs 部署时，此功能是常见的考虑因素。
- **网络服务（虚拟网络、子网）：Azure**[网络](../networking/networking-overview.md)拓扑是 DevTest Labs 体系结构中的另一个关键元素。 它控制实验室的资源是否可以通信和访问本地和互联网。 我们的体系结构图包括客户使用 DevTest Labs 的最常见方式：所有实验室通过使用[中心辐条模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)连接到到本地的 ExpressRoute/站点到站点 VPN 连接，通过[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)进行连接。 但是 DevTest Labs 直接使用 Azure 虚拟网络，因此对如何设置网络基础结构没有限制。
- **开发人员测试实验室**：开发人员测试实验室是整个体系结构的关键部分。 要了解有关该服务的更多内容，请参阅[有关开发人员测试实验室](devtest-lab-overview.md)。
- **虚拟机和其他资源（SaaS、PaaS、IaaS）：** 虚拟机是 DevTest Labs 与其他 Azure 资源一起支持的关键工作负载。 开发人员测试实验室使企业能够轻松快速地提供对 Azure 资源（包括 VM 和其他 Azure 资源）的访问。 详细了解[开发人员](devtest-lab-developer-lab.md)和[测试人员](devtest-lab-test-env.md)访问 Azure。

## <a name="scalability-considerations"></a>可伸缩性注意事项
尽管 DevTest Labs 没有内置配额或限制，但在实验室的典型操作中使用的其他 Azure 资源确实具有[订阅级配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。 因此，在典型的企业部署中，需要多个 Azure 订阅来覆盖 DevTest Labs 的大规模部署。 企业最常达到的配额包括：

- **资源组**：在默认配置中，DevTest Labs 为每个新虚拟机创建一个资源组，或者用户使用该服务创建环境。 订阅最多可以包含[980 个资源组](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 因此，这是订阅中虚拟机和环境的限制。 还有另外两个配置需要考虑：
    - **[所有虚拟机都转到同一资源组](resource-group-control.md)**：尽管此设置可帮助您满足资源组限制，但它会影响资源类型/资源组限制。
    - **使用共享公共 IP：** 相同大小和区域的所有 VM 都进入同一资源组。 如果允许虚拟机具有公共 IP 地址，则此配置是资源组配额和资源类型/资源组配额之间的"中间地带"。
- **每个资源类型的资源组**：[每个资源组每个资源类型的资源的默认限制为 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)。  当您使用所有*VM 转到同一资源组*配置时，用户会更快地达到此订阅限制，尤其是在 VM 具有许多额外磁盘的情况下。
- **存储帐户**：DevTest 实验室中的实验室附带存储帐户。 [每个订阅每个区域的存储帐户数](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)的 Azure 配额为 250 。 同一区域中的 DevTest 实验室的最大数量也是 250。
- **角色分配**：角色分配是授予用户或主体对资源（所有者、资源、权限级别）的访问权限的方式。 在 Azure 中，[每个订阅有 2，000 个角色分配的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits)。 默认情况下，DevTest Labs 服务为每个 VM 创建一个资源组。 所有者被授予开发人员测试实验室 VM*的所有者*权限，向资源组授予*读取器*权限。 这样，您创建的每个新 VM 除了在授予用户实验室权限时使用分配外，还使用两个角色分配。
- **API 读取/写入**：自动执行 Azure 和开发人员测试实验室的方法有多种，包括 REST API、PowerShell、Azure CLI 和 Azure SDK。 通过自动化，您可能会对 API 请求达到另一个限制：每个订阅允许每小时最多[12，000 个读取请求和 1，200 个写入请求](../azure-resource-manager/management/request-limits-and-throttling.md)。 在自动执行 DevTest 实验室时，请注意此限制。

## <a name="manageability-considerations"></a>可管理性注意事项
DevTest Labs 具有出色的管理用户界面，可用于使用单个实验室。 但在企业中，您可能有多个 Azure 订阅和许多实验室。 对所有实验室进行一致的更改需要脚本/自动化。 以下是 DevTest Labs 部署的一些示例和最佳管理实践：

- **对实验室设置的更改**：常见方案是更新部署中所有实验室中的特定实验室设置。 例如，新的 VM 实例大小可用，并且必须更新所有实验室以允许它。 最好使用 PowerShell 脚本、CLI 或 REST API 自动执行这些更改。  
- **项目存储库个人访问令牌**：通常，Git 存储库的个人访问令牌将在 90 天、一年或两年后过期。 为了确保连续性，扩展个人访问令牌或创建新访问令牌非常重要。 然后使用自动化将新的个人访问令牌应用于所有实验室。
- **限制对实验室设置的更改**：通常，必须限制特定设置（例如允许使用市场映像）。 可以使用 Azure 策略来防止对资源类型的更改。 或者，您可以创建自定义角色，并授予用户该角色，而不是实验室*的所有者*角色。 您可以针对实验室中的大多数设置（内部支持、实验室公告、允许的 VM 大小等）执行此操作。
- **要求 VM 遵循命名约定**：经理通常希望轻松识别属于基于云的开发和测试环境的 VM。 可以使用[Azure 策略](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)执行此操作。

请务必注意，DevTest Labs 使用以相同方式管理的基础 Azure 资源：网络、磁盘、计算等。 例如，Azure 策略适用于在实验室中创建的虚拟机。 Azure 安全中心可以报告 VM 合规性。 Azure 备份服务可以为实验室中的 VM 提供定期备份。

## <a name="security-considerations"></a>安全注意事项
Azure 开发人员测试实验室使用 Azure 中的现有资源（计算、网络等）。 因此，它会自动受益于平台中内置的安全功能。 例如，要要求传入的远程桌面连接仅来自公司网络，只需将网络安全组添加到远程桌面网关上的虚拟网络即可。 唯一额外的安全考虑是您授予每天使用实验室的团队成员的权限级别。 最常见的权限是[*所有者*和*用户*](devtest-lab-add-devtest-user.md)。 有关这些角色的详细信息，请参阅在[Azure 开发人员测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[向上扩展 Azure 开发人员测试实验室基础结构](devtest-lab-guidance-scale.md)。
