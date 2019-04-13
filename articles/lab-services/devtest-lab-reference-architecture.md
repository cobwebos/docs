---
title: Azure 开发测试实验室针对企业的参考体系结构
description: 本文提供有关在企业中的 Azure 开发测试实验室参考体系结构指南。
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
ms.openlocfilehash: 61e76369a4d73bd171c9e5c2462b3f261681ba00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551377"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure 开发测试实验室-企业参考体系结构
本文提供参考体系结构部署在企业中的 Azure 开发测试实验室所基于的解决方案。 它包括通过 Express Route 的远程登录到虚拟机的远程桌面网关、 连接到专用的项目，并在实验室中使用其他 PaaS 服务的项目存储库的本地连接。

![参考体系结构](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>体系结构
参考体系结构中的关键要素是：

- **Azure Active Directory (AAD)**:使用 azure 开发测试实验室[用于标识管理的 Azure Active Directory 服务](../active-directory/fundamentals/active-directory-whatis.md)。 有以下两个在提供基于用户的开发测试实验室环境的访问权限时需要考虑的事项：
    - **资源管理**:它提供了 Azure 门户来管理资源的访问权限 （创建虚拟机、 创建环境、 开始/停止/重新启动/删除/applyartifacts，等等）。 这样做是在 Azure 中使用基于 Roble 的访问控制 (RBAC) 和通过应用角色分配用户、 设置资源和访问级别权限。
    - **虚拟机 （网络级别）**:在默认配置中，虚拟机使用的本地管理员帐户。  如果没有可用的域 ([AAD 域服务](../active-directory-domain-services/active-directory-ds-overview.md)，本地域域或基于云的域)，计算机可以加入到域。 联接后，用户将使用其基于域的身份连接到 Vm。
- **本地连接**:更高版本，体系结构关系图[Express Route](../expressroute/expressroute-introduction.md) i 使用，但也可以使用[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 虽然不是必需的用于开发测试实验室，它通常在企业中出现。 它被必需是否连接到公司资源的原因。 常见原因如下： 
    - 不能尚未迁移到云的本地数据
    - 首选项，以便将实验室的虚拟机加入到本地域
    - 强制所有传入和传出通过安全/法规遵从性目的的本地防火墙在云环境的网络流量
- **网络安全组**：若要将流量限制到云环境 （或云环境中） 的常用方法基于源和目标 IP 地址是使用[网络安全组](../virtual-network/security-overview.md)。 例如，允许从企业网络发起到实验室的网络的网络流量。
- **远程桌面网关**:企业通常阻止传出远程桌面连接在企业防火墙。 若要启用连接到开发测试实验室中基于云的环境，有几个选项 （例如） 使用[远程桌面网关](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)（网关负载均衡器的静态 IP 白名单） 或[定向所有传入RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)通过 Express Route/站点到站点 VPN 连接。 规划部署在企业中的开发测试实验室时，它是一个常见的注意事项。
- **Azure 网络 （Vnet、 子网）**:[Azure 网络](../networking/networking-overview.md)拓扑是在整个开发测试实验室体系结构中的另一个关键元素。 它使从实验中进行通信 （与否），访问在本地 （或不） 和 （或不） 可以访问 internet 资源。 体系结构关系图中包括客户使用开发测试实验室的最常见方法 (通过连接的所有实验室[VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)使用[中心辐射型模型](/architecture/reference-architectures/hybrid-networking/hub-spoke)到 Express Route/站点到站点 VPN 连接为在本地），但由于开发测试实验室将使用 Azure 网络直接没有关于如何设置网络基础结构的任何限制。
- **开发测试实验室**:开发测试实验室是整体体系结构的关键部分。 若要了解有关服务，请参阅[有关开发测试实验室](devtest-lab-overview.md)。
- **虚拟机和其他资源 (SaaS、 PaaS、 IaaS)**:支持的开发测试实验室的关键工作负荷之一为虚拟机以及其他 Azure 资源。  开发测试实验室可快捷和方便企业可以让 Azure 资源 （包括虚拟机和其他 Azure 资源） 的访问。  详细了解适用于 Azure 的访问[开发人员](devtest-lab-developer-lab.md)并[测试人员](devtest-lab-test-env.md)。

## <a name="scalability-considerations"></a>可伸缩性注意事项
尽管开发测试实验室不会有任何内置的配额或限制中的典型仍使用其他 Azure 资源的实验室操作都有[订阅级别配额](../azure-subscription-service-limits.md)。 因此，在典型的企业部署中，您必须具有多个 Azure 订阅，以涵盖开发测试实验室的大型部署。 通过企业最常访问的配额是：

- **资源组**：在默认配置中，开发测试实验室创建每个新的虚拟机或用户创建使用服务的环境的资源组。 订阅可以包含[980 资源组的最大](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)，因此此限制是虚拟机和在订阅中的环境的上限。 有两个您应考虑其他配置：
    - **[所有虚拟机都转到同一资源组](resource-group-control.md)**:尽管它有助于您使用的资源组限制，但是它会影响每个资源组限制的资源类型。
    - **使用共享的公共 Ip**:相同的大小和同一区域中的所有 Vm 都进入同一个资源组。 如果虚拟机可以有公共 IP 地址，则介于资源组配额和每个资源组配额的资源类型。 
- **每个资源类型的每个资源的资源组**:默认限制[每个资源组，每个资源类型的资源为 800](../azure-subscription-service-limits.md#resource-group-limits)。  时使用的所有 Vm 时，请转到同一资源组配置，用户将达到此订阅限制更快，尤其是如果的 Vm 具有许多额外的磁盘。
- **存储帐户**：开发测试实验室的实验室中附带了一个存储帐户和 Azure 配额[的每个区域每个订阅的存储帐户数为 250](../azure-subscription-service-limits.md#storage-limits)。 这意味着，在同一区域中的开发测试实验室的数的上限也是 250。
- **角色分配**:角色分配是如何让用户或主体访问资源 （所有者、 资源、 权限级别）。 在那里的 Azure 中，[限制为 2000年个角色分配每个订阅](../azure-subscription-service-limits.md#role-based-access-control-limits)。 开发测试实验室服务 （在使用默认配置） 为每个 VM 创建一个资源组和所有者将被授予**所有者**开发测试实验室 VM 的权限和**读取器**权限资源组。  在这种方式，每个新创建的 VM 使用创建时为用户提供到实验室的权限的角色分配到两个其他角色分配。
- **API 读取/写入**:有多种方法 （REST Api、 PowerShell、 CLI、 Azure SDK 等） 来自动执行 Azure 和开发测试实验室，通过自动化很有可能对 API 请求的另一个限制。 每个订阅最多允许[12000 读取请求和 1200年写入请求每小时](../azure-resource-manager/resource-manager-request-limits.md)。  它是需要注意的自动化开发测试实验室时的限制。

## <a name="manageability-considerations"></a>可管理性注意事项
开发测试实验室使用单个实验室时有一个很好的管理用户界面。 在企业中，可能有多个 Azure 订阅和许多实验。 这意味着，对所有实验室中一致地进行更改需要脚本自动化。  下面是一些示例和开发测试实验室部署进行管理的最佳方法：

- **将更改为实验室设置**:一种常见方案是更新特定实验室设置不同的部署中的所有实验室。 例如，新的 VM 实例大小都可用，所有实验室必须进行都更新以允许它。  最好是通过 Azure PowerShell 脚本、 Azure CLI 或 REST Api 自动执行这些更改。  
- **项目存储库个人访问令牌**:通常情况下，Git 存储库的个人访问令牌过期 （90 天，1 年，2 年）。 为了确保连续性，务必要扩展的个人访问令牌或创建一个新，并使用自动化来将新的个人访问令牌应用于所有实验室。
- **将更改限制为实验室设置**:它通常是这种情况的特定设置 （例如，允许 Marketplace 映像用于） 必须限制。 可以通过 Azure 策略 （防止更改该资源类型的） 或通过创建自定义角色，并授予该角色而不是到实验室的所有者。 可能会出于大部分 （内部支持、 实验室通告、 允许虚拟机大小等） 在实验室中设置
- **需要 Vm 遵循命名约定**:这是一个常见的请求，可以轻松地识别是基于云的开发和测试环境的一部分的 Vm。 就能做到[与 Azure 策略](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)。

请务必注意开发测试实验室使用 Azure （网络、 磁盘、 计算、 等等） 中管理 Azure 中的相同方式的基础资源。  例如，Azure 策略适用于在实验室中创建的虚拟机。 Azure 安全中心可以针对 VM 合规性报告。 Azure 备份服务可以在实验室中为 Vm 提供定期备份等。 

## <a name="security-considerations"></a>安全注意事项
Azure 开发测试实验室在 Azure （计算、 网络等） 中使用现有的资源，并因此自动可以利用所有最大的安全性功能内置到平台。 例如，若要保护任何传入的远程桌面连接为仅来自公司网络，它只需将网络安全组添加到在远程桌面网关的虚拟网络。 Azure 开发测试实验室的唯一附加的安全注意事项是提供给团队成员将按一天的实验室使用权限的级别。  给定公用权限有["所有者"和"开发测试实验室用户"](devtest-lab-add-devtest-user.md)。 有关这些角色的详细信息，请参阅[Azure 开发测试实验室中添加所有者和用户](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[纵向扩展 Azure 开发测试实验室基础结构](devtest-lab-guidance-scale.md)
