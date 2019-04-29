---
title: 纵向扩展 Azure 开发测试实验室基础结构
description: 本文提供有关纵向扩展 Azure 开发测试实验室基础结构的指导。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561483"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>纵向扩展 Azure 开发测试实验室基础结构
在以企业规模实施开发测试实验室之前，有几个关键的决策点。 组织大致了解这些决策点有助于将来做出设计决策。 但是，这些要点不应阻碍组织的概念证明工作。 初始纵向扩展规划的三个首要方面包括：

- 网络和安全性
- 订阅拓扑
- 角色和职责

## <a name="networking-and-security"></a>网络和安全性
网络和安全性是所有组织的基石。 尽管企业范围的部署所需的分析要深入得多，但成功实现概念证明所要满足的要求较少。 部分重要关注面包括：

- **Azure 订阅** – 若要部署开发测试实验室，必须有权访问 Azure 订阅，并且有权创建资源。 可通过多种方法获取 Azure 订阅的访问权限，包括企业协议和即用即付。 有关获取 Azure 订阅访问权限的详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。
- **本地资源的访问权限** - 某些组织要求开发测试实验室中的资源有权访问本地资源。 需要建立从本地环境到 Azure 的安全连接。 因此，在开始之前，设置/配置 VPN 或 Express Route 连接非常重要。 有关详细信息，请参阅[虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
- **其他安全要求** – 计算机策略、访问公共 IP 地址、连接到 Internet 等其他安全要求是在实施概念证明之前可能需要审查的方案。 

## <a name="subscription-topology"></a>订阅拓扑
在企业中部署开发测试实验室时，订阅拓扑是关键的设计考虑因素。 但是，只有在完成概念证明之后，才需要强化所有决策。 评估企业实施方案所需的订阅数时，存在两种极端： 

- 整个组织只有一个订阅
- 用户各有订阅

接下来，我们将重点分析每种方法的利弊。

### <a name="one-subscription"></a>一个订阅
通常，采用一个订阅的方法在大型企业中不易管理。 但是，限制订阅数可提供以下优势：

- **预测**企业的成本。  使用单个订阅能使预算变得非常容易，因为所有资源都在一个池中。 使用此方法可以在计费周期内的任何给定时间更方便地决定何时行使成本控制措施。
- **可管理性**的 Vm、 项目、 公式、 网络配置、 权限、 策略，等则简单因为所有的更新仅需要一个订阅，而不是进行跨多个订阅的更新中。
- 对于要求使用本地连接的企业而言，单个订阅中的**网络**工作可以大大简化。 使用其他订阅时，必须跨订阅连接虚拟网络（中心辐射模型），这就需要额外的配置、管理、IP 地址空间，等等。
- 每个人在同一订阅中操作可以简化**团队协作** – 例如，可以更轻松地将 VM 重新分配给同事、共享资源团队，等等。

### <a name="subscription-per-user"></a>用户各有订阅
从备选范围来看，每个用户使用单独的订阅可以提供均等的机会。 使用多个订阅的优势包括：

- **Azure 缩放配额**不会阻碍解决方案的采用。 例如，在撰写本文时，Azure 允许为每个订阅创建 200 个存储帐户。 Azure 中的大多数服务都有运行配额（有些服务可自定义，有些则不可以）。 在用户各有订阅模型中，达到大部分配额的可能性很低。 有关当前 Azure 缩放配额的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。
- 组或单个开发人员的**费用分摊**会容易得多，使组织能够使用其当前模型考量成本。
- 开发测试实验室环境的**所有权和权限**非常简单。 为开发人员授予订阅级访问权限，他们将完全负责所有工作，包括网络配置、实验室策略和 VM 管理。

企业可能对选择范围的极端施加了足够的约束。 因此，可能需要在这些极端的中间位置设置订阅。 作为最佳做法，组织的目标应该是尽量少用订阅，同时注意会增加订阅总数的外力因素。 重申一下，订阅拓扑对于企业部署开发测试实验室至关重要，但不应延缓概念证明。 有关如何在组织中确定订阅和实验室粒度的[监管](devtest-lab-guidance-governance-policy-compliance.md)文章提供了更多详细信息。

## <a name="roles-and-responsibilities"></a>角色和职责
开发测试实验室概念证明包括三个界定了职责的主要角色 – 订阅所有者、开发测试实验室所有者、开发测试实验室用户和可选的参与者。

- **订阅所有者** – 订阅所有者有权管理 Azure 订阅，包括分配用户、管理策略、创建和管理网络拓扑、请求提高配额，等等。有关详细信息，请参阅[此文章](../role-based-access-control/rbac-and-directory-admin-roles.md)。
- **开发测试实验室所有者** – 开发测试实验室所有者对实验室拥有完全管理访问权限。 此人负责添加/删除用户、管理成本设置、常规实验室设置和其他基于 VM/项目的任务。 实验室所有者还拥有开发测试实验室用户的所有权限。
- **开发测试实验室用户** – 开发测试实验室用户可以在实验室中创建和使用虚拟机。 这些人可以在他们创建的 VM 上执行一些很次要的管理功能（启动/停止/删除/配置其 VM）。 这些用户不能管理其他用户的 VM。

## <a name="next-steps"></a>后续步骤
请参阅本系列的下一篇文章：[协调 Azure 开发测试实验室的实施](devtest-lab-guidance-orchestrate-implementation.md)