---
title: 提供概念证明-Azure 开发测试实验室 |Microsoft Docs
description: 了解如何提供概念证明, 以便 Azure 开发测试实验室可以成功纳入企业环境中。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 13e3f6be851e81b1186d55bb313dd23f1920f007
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616353"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念证明 

Azure 开发测试实验室的重要方案之一是在云中启用开发和测试环境。 示例包括：

* 在云中创建开发人员桌面。
* 配置环境以进行测试。
* 启用对虚拟机和其他 Azure 资源的访问。
* 设置沙盒区域以便开发人员学习和试验。

开发测试实验室还提供策略 guardrails 和成本控制, 使企业能够向开发人员提供 "自助服务 Azure", 以符合公司的安全、法规和合规性策略。 

每个企业都有不同的要求, 即如何将 Azure 开发测试实验室成功融入到其环境中。 本文介绍企业需要完成的最常见步骤, 以确保成功的概念证明。 概念证明是成功完成端到端部署的第一步。 

## <a name="getting-started"></a>入门 

开始提供概念证明。 必须花些时间来了解 Azure 和开发测试实验室。  下面是一些开始资源: 

* [了解 Azure 门户](https://azure.microsoft.com/features/azure-portal/)
* [开发测试实验室基础知识](devtest-lab-overview.md)
* [开发测试实验室支持的方案](devtest-lab-guidance-get-started.md)
* [开发测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 网络简介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>先决条件 

若要使用开发测试实验室成功完成试验或概念证明, 有几个前提条件: 

* **Azure 订阅**：企业通常有一个现有的[企业协议](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/), 可实现对 Azure 的访问权限, 并且他们可以使用现有的或新的开发测试实验室订阅。 或者, 企业可以在试验期间使用[Visual Studio 订阅](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)(利用免费的 Azure 额度)。 如果这两个选项都不可用, 则企业可以创建和使用[免费的 Azure 帐户](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有企业协议, 则使用[企业开发/测试订阅](https://azure.microsoft.com/offers/ms-azr-0148p/)是获取对 Windows 10/Windows 8.1 客户端操作系统的访问权限以及开发和测试工作负荷的折扣率的不错选择。 
* **Azure Active Directory 租户**:若要启用管理用户 (例如, 添加用户或添加实验室所有者), 这些用户必须是用于试验的 Azure 订阅中使用的[Azure Active Directory 租户](https://azure.microsoft.com/services/active-directory/)的一部分。 企业通常会设置[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md), 使用户能够在云中使用本地标识, 但这不是开发测试实验室试点项目所必需的。 

## <a name="scoping-of-the-pilot"></a>试验范围 

开始实施之前, 请务必规划试点。 事先知道, 资源不会持续下去, 为试验用户设置适当的期望。 

> [!IMPORTANT]
> 我们无法强调直接作用域的重要性, 并预先设置期望值。

开始试验之前, 请先回答以下重要问题: 

* 您想学习什么内容, 哪些功能对于试验成功？ 
* 试点会涵盖哪些工作负荷或场景？ 必须仅定义一个小集, 以确保可以快速确定试验范围并完成。 
* 实验室中必须有哪些资源？ 例如: 自定义映像、marketplace 映像、策略和网络拓扑。 
* 谁是将参与试点来验证体验的用户和团队？  
* 试点的持续时间是多少？ 选择适合于计划范围的时间范围, 如两周或一个月。 
* 试点完成后, 在试验期间使用的已分配资源将发生什么情况？ 你是否打算放弃试点资源？ 你可能会认为:
   
   "如果我们可以计划在试点结束时丢弃虚拟机和实验室, 我们可以为试验设置单个订阅, 并在此过程中完成所有工作, 同时解决大规模推出问题。" 

进行试验 "完美" 的一般趋势, 使其与在公司推出服务后的最终状态完全相同。 这是一个假假设。 您越接近 "完美", 您就可以在开始试验*之前*完成更多的工作。 试验的目的是做出正确的决定, 以便扩展和推出最终服务。 

试验的重点应该是选择最少的必要工作负荷和依赖项, 以便回答 Azure 开发测试实验室是否适用于企业的合适服务。 建议选择具有最少依赖项的最简单的工作负荷, 以帮助确保快速而干净的成功。 如果无法做到这一点, 请选择一种最具代表性的工作负载来公开潜在的复杂性, 以便可以在扩展阶段复制试验阶段的成功。 

下面的示例演示了一种确定作用域的概念证明。 

## <a name="example-proof-of-concept-plan"></a>示例: 概念验证计划 

本部分演示了一个示例, 该示例用于限定开发测试实验室试验的概念证明。 

> [!TIP]
> 为了尽量减少将项目设置为失败的可能性, 强烈建议您不要跳过此部分中所述的示例。 

### <a name="overview"></a>概述 

我们计划在 Azure 中开发一个新的环境, 使其基于开发测试实验室, 供供应商用作企业网络的独立环境。 若要确定解决方案是否满足要求, 我们将开发概念证明来验证端到端解决方案。 我们提供了几个供应商来试用并验证体验。 

### <a name="outcomes"></a>申请人 

在构建概念证明时, 我们首先关注结果 (我们尝试实现哪些目标)。 在概念证明结束时, 我们期待: 

* 一款适用于供应商的端到端解决方案, 使用 Azure Active Directory (Azure AD) 中的来宾帐户来访问 Azure 中的隔离环境。 环境具有其工作效率所需的资源。 
* 会对任何影响广泛使用和采用的潜在阻碍性问题进行枚举和理解。
* 开发概念证明所涉及的人员对所有代码都有充分的了解。 他们还了解所涉及的宣传品, 并自信地采用更广泛的采用。

### <a name="open-questions-and-prerequisites"></a>打开问题和先决条件 

* 是否创建了可用于此项目的订阅？ 
* 是否有 Azure AD 租户和 Azure AD 全局管理员身份确定了谁可以为与 Azure AD 相关的问题提供帮助和指导？ 
* 我们有一个用于协作处理项目的人员吗？ 

   * 源代码和脚本 (如 Azure Repos) 
   * 文档 (如 Microsoft 团队或 SharePoint)  
   * 会话 (如 Microsoft 团队) 
   * 工作项 (如 Azure Boards) 
* 供应商需要哪些资源？ 这包括网络上可用的应用程序, 这些应用程序在本地虚拟机上和其他所需的服务器上可用。 
* 虚拟机是否会加入 Azure 中的域？ 如果是, 这是 Azure Active Directory 域服务 (Azure AD DS) 还是其他内容？ 
* 是否确定了将成为概念证明目标的团队或供应商？ 谁将成为该环境的客户？
* 我们将使用哪个 Azure 区域来进行概念证明？ 
* 除了 IaaS (Vm) 以外, 还可以通过开发测试实验室来使用供应商提供的服务列表？ 
* 我们如何计划开发供应商/用户使用实验室？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念证明解决方案的组成部分 

我们希望解决方案具有以下组件: 

* 各种供应商团队将在 Azure 中使用一组实验室。
* 实验室连接到支持要求的网络基础结构。
* 供应商可以通过 Azure AD 和角色分配来访问实验室。
* 供应商可以成功连接到其资源。 具体而言, 站点到站点 VPN 可直接访问没有公共 IP 地址的虚拟机。
* 一组项目包括供应商在虚拟机上所需的软件。

## <a name="additional-planning-and-design-decisions"></a>其他规划和设计决策 

在发布完整的开发测试实验室解决方案之前, 必须做出一些重要的规划和设计决策。 使用概念证明的经验有助于做出这些决策。 其他注意事项包括: 

* **订阅拓扑**:Azure 中资源的企业级要求可以超出[单个订阅中的可用配额](https://docs.microsoft.com/azure/azure-subscription-service-limits)。 这需要多个 Azure 订阅和/或服务请求以增加初始订阅限制。 务必提前决定如何将资源分布到多个订阅。 一个有价值的资源是[订阅决策指南](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/), 因为以后很难将资源移到另一个订阅。 例如, 在创建实验室后, 无法将其移动到另一个订阅。  
* **网络拓扑**:开发测试 Labs 自动创建的[默认网络基础结构](../app-service/networking-features.md)可能不足以满足企业用户的要求和限制。 常见的情况是, 可以查看[Azure ExpressRoute 连接的虚拟网络](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)、跨订阅连接的[中心辐射](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), 甚至[强制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)以确保仅本地连接。 开发测试实验室允许将现有虚拟网络连接到实验室, 以便在实验室中创建新的虚拟机时使用。 
* **虚拟机的远程访问**:有很多选项可用于远程访问位于开发测试实验室中的虚拟机。 最简单的方法是使用公共 Ip 或共享公共 Ip。 这些是[实验室中可用的设置](devtest-lab-shared-ip.md)。 如果这些选项不足, 还可以选择使用远程访问网关。 此选项显示在[开发测试实验室企业参考体系结构](devtest-lab-reference-architecture.md)上, 并在[开发测试实验室远程桌面网关文档](configure-lab-remote-desktop-gateway.md)中进行了进一步说明。 企业还可以使用 ExpressRoute 或站点到站点 VPN 将其实验室连接到本地网络。 此选项可根据虚拟机的专用 IP 地址, 实现到这些虚拟机的直接远程桌面或 SSH 连接, 而不会向 internet 公开。 
* **处理权限**:开发测试实验室中通常使用的两个主要权限是[所有者和实验室用户](devtest-lab-add-devtest-user.md)。 在推出开发测试实验室之前做出决定, 这一点很重要, 他们将获得实验室中每个级别的访问权限。 常见的模型是预算所有者 (例如, 团队主管) 作为实验室所有者, 将团队成员作为实验室用户。 此模型使负责预算的人员 (团队主管) 能够调整策略设置并使团队保持在预算内。  

## <a name="completing-the-proof-of-concept"></a>完成概念证明 

在涵盖预期的知识后, 就可以完成试验了。 这是收集用户反馈的时间, 确定试运行是否成功, 并决定组织是否会在企业中的开发测试实验室规模推出后继续进行。 这也是一个很好的时间, 可以考虑自动部署开发测试实验室和关联的资源, 以确保整个规模部署的一致性。 

## <a name="next-steps"></a>后续步骤 

* [开发测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [企业的参考体系结构](devtest-lab-reference-architecture.md)
* [扩展开发测试实验室部署](devtest-lab-guidance-orchestrate-implementation.md)
* [协调 Azure 开发测试实验室的实施](devtest-lab-guidance-orchestrate-implementation.md)
