---
title: 针对 Azure 开发测试实验室的企业参考体系结构
description: 本文提供了有关 Azure 开发测试实验室在企业中的参考体系结构指南。
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
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244716"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>适用于企业的 azure 开发测试实验室参考体系结构
本文提供了参考体系结构可帮助你部署基于 Azure 开发测试实验室在企业中的解决方案。 它包括以下组件：
- 通过 Azure ExpressRoute 的本地连接
- 远程桌面网关，若要远程登录到虚拟机
- 连接到专用项目的项目存储库
- 在实验室中使用其他 PaaS 服务

![参考体系结构关系图](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>体系结构
这些是参考体系结构的关键元素：

- **Azure Active Directory (Azure AD)** ：使用开发测试实验室[用于标识管理的 Azure AD 服务](../active-directory/fundamentals/active-directory-whatis.md)。 您让用户访问基于开发测试实验室环境时，请考虑这两个关键方面：
    - **资源管理**:它提供了 Azure 门户来管理资源的访问权限 （创建虚拟机; 创建环境; 启动、 停止、 重新启动、 删除和应用项目; 和其他操作）。 资源管理可在 Azure 中通过使用基于角色的访问控制 (RBAC)。 向用户分配角色并设置资源和访问级别的权限。
    - **虚拟机 （网络级别）** :在默认配置中，虚拟机使用的本地管理员帐户。 如果没有可用的域 ([Azure AD 域服务](../active-directory-domain-services/overview.md)，本地域域或基于云的域)，计算机可以加入到域。 然后，用户可以使用其基于域的身份连接到 Vm。
- **本地连接性**：在我们的体系结构关系图中， [ExpressRoute](../expressroute/expressroute-introduction.md)使用。 但也可以使用[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 尽管 ExpressRoute 不是用于开发测试实验室所需的但它通常在企业中使用。 ExpressRoute 是所需的仅在需要对公司资源的访问。 常见方案是：
    - 你有不能移动到云的本地数据。
    - 您倾向于实验室的虚拟机加入到本地域。
    - 你想要强制所有传入和传出通过安全/法规遵从性的本地防火墙在云环境的网络流量。
- **网络安全组**：若要将流量限制到云环境 （或云环境中） 的常用方法基于源和目标 IP 地址是使用[网络安全组](../virtual-network/security-overview.md)。 例如，你想要允许来自企业网络到实验室的网络的流量。
- **远程桌面网关**:企业通常阻止传出远程桌面连接在企业防火墙。 有几个选项，以便连接到基于云的环境中开发测试实验室，其中包括：
  - 使用[远程桌面网关](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，和允许列表的网关的静态 IP 地址的负载均衡器。
  - [将所有传入的 RDP 流量定向](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)通过 ExpressRoute/站点到站点 VPN 连接。 企业计划的开发测试实验室部署时，此功能是一个常见的注意事项。
- **网络服务 （虚拟网络、 子网）** :[Azure 网络](../networking/networking-overview.md)拓扑是在开发测试实验室体系结构中的另一个关键要素。 它控制从实验室的资源是否可进行通信并有权访问的本地和 internet。 我们的体系结构关系图中包括客户使用开发测试实验室的最常见方式：通过连接所有实验室[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)通过使用[中心辐射型模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)向本地 ExpressRoute/站点到站点 VPN 连接。 但是，开发测试实验室使用 Azure 虚拟网络直接，因此没有关于如何设置网络基础结构限制。
- **开发测试实验室**:开发测试实验室是整体体系结构的关键部分。 若要了解有关该服务的详细信息，请参阅[有关开发测试实验室](devtest-lab-overview.md)。
- **虚拟机和其他资源 (SaaS、 PaaS、 IaaS)** :虚拟机是开发测试实验室支持以及其他 Azure 资源的关键工作负荷。 开发测试实验室，就能轻松快速为企业提供对 Azure 资源 （包括虚拟机和其他 Azure 资源） 的访问。 详细了解适用于 Azure 的访问[开发人员](devtest-lab-developer-lab.md)并[测试人员](devtest-lab-test-env.md)。

## <a name="scalability-considerations"></a>可伸缩性注意事项
虽然开发测试实验室不具有内置的配额或限制，确实具有典型操作的实验室中使用其他 Azure 资源[订阅级别配额](../azure-subscription-service-limits.md)。 因此，在典型的企业部署中，您需要多个 Azure 订阅，以涵盖开发测试实验室的大型部署。 企业最常访问的配额是：

- **资源组**：在默认配置中，开发测试实验室创建每个新的虚拟机的资源组或用户通过使用服务创建一个环境。 订阅可以包含[980 最多的资源组](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)。 这就是虚拟机和在订阅中的环境的限制。 有两个您应考虑其他配置：
    - **[所有虚拟机都转到同一资源组](resource-group-control.md)** :尽管此安装程序可帮助你满足资源组限制，但它会影响的资源类型每个资源组限制。
    - **使用共享的公共 Ip**:在相同的大小和区域中的所有 Vm 都进入同一个资源组。 如果虚拟机可以有公共 IP 地址，此配置会"介于"资源组配额和资源类型每个资源组配额。
- **每个资源的资源组的每个资源类型**:默认限制[每个资源组，每种资源类型的资源为 800](../azure-subscription-service-limits.md#resource-group-limits)。  当你使用*转到相同的资源组的所有 Vm*配置、 用户命中此订阅限制太多会更快，尤其是如果的 Vm 具有许多额外的磁盘。
- **存储帐户**：开发测试实验室的实验室中附带了一个存储帐户。 Azure 配额[的每个区域每个订阅的存储帐户数为 250](../azure-subscription-service-limits.md#storage-limits)。 开发测试实验室在同一区域中的最大数目也是 250。
- **角色分配**:角色分配是如何向用户或主体的访问权限授予对资源 （所有者、 资源、 权限级别）。 在 Azure 中，没有[限制为 2,000 角色分配，每个订阅](../azure-subscription-service-limits.md#role-based-access-control-limits)。 默认情况下，开发测试实验室服务为每个 VM 创建资源组。 所有者授予*所有者*开发测试实验室 VM 的权限和*读取器*到资源组的权限。 在这种方式，创建每个新 VM 使用除了时用户被授予权限的实验室使用的分配的两个角色分配。
- **API 读取/写入**:有多种方法来自动执行 Azure 和开发测试实验室，其中包括 REST Api、 PowerShell、 Azure CLI 和 Azure SDK。 通过实现自动化，你可能会遇到另一个 API 请求限制：每个订阅最多允许[12,000 读取请求和 1200 写入请求每小时](../azure-resource-manager/resource-manager-request-limits.md)。 当自动开发测试实验室时，则请注意此限制。

## <a name="manageability-considerations"></a>可管理性注意事项
开发测试实验室具有出色的管理用户界面用于处理单个实验室。 但在企业中，您可能有多个 Azure 订阅和许多实验。 对所有实验室一致地进行更改需要脚本自动化。 下面是一些示例和开发测试实验室部署的最佳管理实践：

- **将更改为实验室设置**:一种常见方案是更新特定实验室设置不同的部署中的所有实验室。 例如，新的 VM 实例大小不可用，并且必须更新所有实验，使其。 最好是通过 PowerShell 脚本、 CLI 或 REST Api 自动执行这些更改。  
- **项目存储库个人访问令牌**:通常情况下，在 90 天、 一年或两年后过期的 Git 存储库的个人访问令牌。 为了确保连续性，务必要扩展的个人访问令牌或创建一个新。 然后使用自动化将新的个人访问令牌应用于所有实验室。
- **更改限制为实验室设置**:通常情况下，特定的设置必须限制 （如允许使用 marketplace 映像）。 可以使用 Azure 策略以防止对资源类型的更改。 也可以创建自定义角色，并向用户授予该角色而不是*所有者*实验室的角色。 您可以执行此操作 （内部支持、 实验室通告、 允许虚拟机大小等） 在实验室中的大多数设置。
- **需要要遵循的命名约定的 Vm**:管理器通常想要轻松识别的基于云的开发和测试环境的一部分的 Vm。 您可以执行此操作通过使用[Azure 策略](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)。

务必要注意开发测试实验室使用的相同的方式管理基础 Azure 资源： 网络、 磁盘、 计算和等等。 例如，Azure 策略适用于在实验室中创建的虚拟机。 Azure 安全中心可以针对 VM 合规性报告。 而 Azure 备份服务可以提供用于在实验室中 Vm 的定期备份。

## <a name="security-considerations"></a>安全注意事项
Azure 开发测试实验室在 Azure （计算、 网络等） 中使用现有资源。 因此它会自动受益于平台内置的安全功能。 例如，如果需要传入远程桌面连接，为仅来自公司网络，请只是网络安全组添加到在远程桌面网关的虚拟网络。 唯一附加安全考虑因素是向团队成员在日常工作中使用实验室的用户授予的权限级别。 最常见的权限[*所有者*并*用户*](devtest-lab-add-devtest-user.md)。 有关这些角色的详细信息，请参阅[Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[纵向扩展 Azure 开发测试实验室基础结构](devtest-lab-guidance-scale.md)。
