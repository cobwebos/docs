---
title: 提供概念验证 - Azure 开发人员测试实验室 |微软文档
description: 了解如何提供概念验证，以便 Azure 开发人员测试实验室可以成功集成到企业环境中。
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
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643281"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念证明 

Azure 开发人员测试实验室的关键方案之一是在云中启用开发和测试环境。 示例包括：

* 在云中创建开发人员桌面。
* 配置用于测试的环境。
* 启用对虚拟机和其他 Azure 资源的访问。
* 设置一个沙盒区域，供开发人员学习和实验。

DevTest Labs 还提供策略保护栏和成本控制，使企业能够向遵守公司安全、法规和合规性策略的开发人员提供"自助服务 Azure"。 

每个企业对于如何成功地将 Azure 开发人员测试实验室集成到其环境中有不同的要求。 本文介绍了企业为确保概念验证成功而需要完成的最常见步骤。 概念证明是成功进行端到端部署的第一步。 

## <a name="getting-started"></a>入门 

开始提供概念证明。 花一些时间了解 Azure 和开发人员测试实验室非常重要。  下面是一些起始资源： 

* [了解 Azure 门户](https://azure.microsoft.com/features/azure-portal/)
* [开发测试实验室基础知识](devtest-lab-overview.md)
* [开发人员测试实验室支持的方案](devtest-lab-guidance-get-started.md)
* [开发人员测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 网络简介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>先决条件 

要通过 DevTest Labs 成功完成试验或概念验证，有以下几个先决条件： 

* **Azure 订阅**：企业通常拥有支持访问 Azure 的现有[企业协议](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)，并且可以使用 DevTest Labs 的现有订阅或新订阅。 或者，企业可以在试验期间使用[Visual Studio 订阅](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)（利用免费的 Azure 积分）。 如果这两个选项都不可用，则企业可以创建和使用[免费的 Azure 帐户](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有企业协议，使用[企业开发人员/测试订阅](https://azure.microsoft.com/offers/ms-azr-0148p/)是访问 Windows 10/Windows 8.1 客户端操作系统和开发和测试工作负载的折扣率的绝佳选择。 
* **Azure 活动目录租户**：要启用管理用户（例如，添加用户或添加实验室所有者），这些用户必须是试用版 Azure 订阅中使用的[Azure 活动目录租户](https://azure.microsoft.com/services/active-directory/)的一部分。 通常，企业会设置[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)，使用户能够在云中使用其本地标识，但 DevTest Labs 试验不需要这样做。 

## <a name="scoping-of-the-pilot"></a>试点范围 

在开始实施之前规划试验非常重要。 提前知道资源不会无限期地存在，这为试点用户设定了适当的期望。 

> [!IMPORTANT]
> 我们再强调一下，明确确定试点范围和预先设定期望值的重要性是不够的。

在启动飞行员之前回答以下关键问题： 

* 你想学什么，成功对飞行员来说是什么样子的？ 
* 试验将涵盖哪些工作负载或方案？ 请务必仅定义一个小型集，以确保可以快速确定试验的范围并完成。 
* 实验室中必须提供哪些资源？ 例如：自定义映像、市场映像、策略、网络拓扑。 
* 谁将参与试点以验证体验的用户和团队？  
* 飞行员的持续时间是多少？ 选择与计划范围很好地一致的时间范围，例如两周或一个月。 
* 试验完成后，在试验期间使用的已分配资源会发生什么情况？ 您是否计划放弃试验资源？ 您可能会认为：
   
   "如果我们可以在试验结束时计划放弃虚拟机和实验室，那么我们可以为试点设置单个订阅，并在那里完成所有工作，同时并行解决规模部署问题。 

有一种普遍的趋势，使试点"完美"，所以它同样代表最终状态后，服务将在公司推出。 这是一个错误的假设。 离"完美"越近，在开始试航*之前*，你越需要完成。 试点的目的是在扩大和推出最终服务方面做出正确的决定。 

试点的重点应该是选择最起码的必需工作负载和依赖项，以便回答 Azure DevTest Labs 是否适合您的企业服务的问题。 我们建议您选择依赖性最小的最简单的工作负载，以帮助确保快速、干净地成功。 如果这是不可能的，选择最具代表性的工作负载，以暴露潜在的复杂性，以便在横向扩展阶段复制试验阶段的成功。 

下面的示例演示了范围广的概念证明。 

## <a name="example-proof-of-concept-plan"></a>示例：概念验证计划 

本节显示了用于界定 DevTest Labs 试验概念验证的示例。 

> [!TIP]
> 为了最大程度地将项目设置为失败的可能性，我们强烈建议您不要跳过本节中描述的示例。 

### <a name="overview"></a>概述 

我们计划在 Azure 中开发一个基于 DevTest Labs 的新环境，供供应商用作与企业网络隔离的环境。 为了确定解决方案是否满足要求，我们将开发一个概念验证来验证端到端解决方案。 我们包括几个供应商来尝试和验证体验。 

### <a name="outcomes"></a>结果 

构建概念验证时，我们首先关注结果（我们试图实现什么）。 在概念证明结束时，我们期望： 

* 供应商使用 Azure 活动目录 （Azure AD） 中的来宾帐户访问 Azure 中的隔离环境的工作端到端解决方案。 环境具有高效工作所需的资源。 
* 列举和理解影响更广泛使用和采用的任何潜在阻塞问题。
* 参与开发概念证明的个人对所有代码都有很好的了解。 他们还了解所涉及的附带关系，并有信心更广泛地采用。

### <a name="open-questions-and-prerequisites"></a>开放式问题和先决条件 

* 我们是否创建了可用于此项目的订阅？ 
* 我们是否确定了 Azure AD 租户和 Azure AD 全局管理员，谁可以为 Azure AD 相关问题提供帮助和指导？ 
* 我们有地方为从事该项目的个人进行合作吗？ 

   * 源代码和脚本（如 Azure 存储库） 
   * 文档（如微软团队或 SharePoint）  
   * 对话（如微软团队） 
   * 工作项（如 Azure 板） 
* 供应商需要哪些资源？ 这包括网络上可用的应用程序，无论是在虚拟机和其他必需服务器上的本地应用程序。 
* 虚拟机是否会加入到 Azure 中的域？ 如果是，这将是 Azure 活动目录域服务 （Azure AD DS） 或其他内容？ 
* 我们是否确定了将成为概念证明目标的团队或供应商？ 谁将是环境的客户？
* 我们将使用哪个 Azure 区域来验证概念？ 
* 除了 IaaS （VM）之外，我们是否还有供应商允许通过 DevTest Labs 使用的服务列表？ 
* 我们计划如何培训供应商/用户使用实验室？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念验证解决方案的组成部分 

我们期望解决方案具有以下组件： 

* 各种供应商团队将在 Azure 中使用一组实验室。
* 实验室连接到支持这些要求的网络基础结构。
* 供应商可以通过 Azure AD 和角色分配访问实验室。
* 供应商有一种方法来成功连接到其资源。 具体来说，站点到站点 VPN 允许在没有公共 IP 地址的情况下直接访问虚拟机。
* 一组工件涵盖了供应商在虚拟机上所需的软件。

## <a name="additional-planning-and-design-decisions"></a>其他规划和设计决策 

在发布完整的 DevTest Labs 解决方案之前，您必须做出一些重要的规划和设计决策。 研究概念证明的经验可以帮助您做出这些决策。 进一步考虑包括： 

* **订阅拓扑**：Azure 中资源的企业级要求可以扩展到[单个订阅中的可用配额之外](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 这需要多个 Azure 订阅和/或服务请求来增加初始订阅限制。 预先决定如何在订阅之间分配资源非常重要。 一个有价值的资源是[订阅决策指南](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)，因为以后很难将资源移动到另一个订阅。 例如，实验室在创建后无法移动到其他订阅。  
* **网络拓扑**：DevTest Labs 自动创建的[默认网络基础结构](../app-service/networking-features.md)可能不足以满足企业用户的要求和约束。 通常可以看到[Azure ExpressRoute 连接的虚拟网络](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)、跨订阅连接[的集线器和分支](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，甚至[强制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)以确保仅本地连接。 DevTest Labs 允许将现有虚拟网络连接到实验室，以便在您在实验室中创建新虚拟机时启用这些网络。 
* **虚拟机的远程访问**：有许多选项可以远程访问位于 DevTest Labs 中的虚拟机。 最简单的是使用公共 IP 或共享公共 IP。 这些是[实验室中可用的设置](devtest-lab-shared-ip.md)。 如果这些选项不够，则使用远程访问网关也是一个选项。 此选项显示在[DevTest Labs 企业参考体系结构](devtest-lab-reference-architecture.md)上，并在[DevTest Labs 远程桌面网关文档中](configure-lab-remote-desktop-gateway.md)进一步描述。 企业还可以使用 ExpressRoute 或站点到站点 VPN 将其实验室连接到其本地网络。 此选项允许基于虚拟机的专用 IP 地址直接连接到虚拟机，无需暴露 Internet。 
* **处理权限**：开发人员测试实验室中常用的两个密钥权限是[所有者和实验室用户](devtest-lab-add-devtest-user.md)。 在广泛推出 DevTest Labs 之前，必须决定谁将受委托在实验室中进行每个级别的访问。 常见模型是作为实验室所有者的预算所有者（例如，团队主管）和作为实验室用户的团队成员。 此模型使负责预算的人员（团队领导）能够调整策略设置，并使团队保持在预算之内。  

## <a name="completing-the-proof-of-concept"></a>完成概念证明 

完成预期学习后，是时候完成试验了。 现在是时候收集用户的反馈，确定试验是否成功，并决定组织是否会在企业中大规模推出 DevTest Labs。 这也是考虑自动部署 DevTest 实验室和相关资源以确保整个规模部署的一致性的好时机。 

## <a name="next-steps"></a>后续步骤 

* [开发人员测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [企业参考体系结构](devtest-lab-reference-architecture.md)
* [扩展开发测试实验室部署](devtest-lab-guidance-orchestrate-implementation.md)
* [协调 Azure 开发测试实验室的实施](devtest-lab-guidance-orchestrate-implementation.md)
