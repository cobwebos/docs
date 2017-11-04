---
title: "Azure 堆栈计划、 产品/服务、 配额，和订阅概述 |Microsoft 文档"
description: "作为云操作员，我想要了解 Azure 堆栈计划、 产品/服务、 配额和订阅。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: erikje
ms.openlocfilehash: 083ca2f0a06625810d2f90a682ba0b3110032e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="plan-offer-quota-and-subscription-overview"></a>计划、产品/服务、配额和订阅概述

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

[Azure 堆栈](azure-stack-poc.md)允许你提供的各种服务，像虚拟机、 SQL Server 数据库、 SharePoint、 Exchange、，甚至[Azure 应用商店项](azure-stack-marketplace-azure-items.md)。 Azure 堆栈运算符，你将配置并通过使用计划、 服务和配额 Azure 堆栈中提供此类服务。

提供包含一个或多个计划，并且每个计划包括一个或多个服务。 通过创建计划并将它们组合成不同产品/服务，你控制
- 可访问的服务和资源的用户
- 用户可以使用这些资源量
- 哪些区域有权访问资源

当你提供服务时，你将遵循以下高级步骤：

1. 添加你想要将传送到你的用户的服务。
2. 创建包含一个或多个服务计划。 在创建计划时，你将选择或创建定义计划中的每个服务的资源限制的配额。
3. 创建产品包含一个或多个计划 （包括基本计划和可选的外接程序计划）。

创建提议后，你的用户可以订阅它来访问服务和它所提供的资源。 用户可以根据需要任意多个提供订阅。 下图显示两个产品具有订阅的用户的简单示例。 每个产品具有一个计划或两个，并且每个计划将给予他们访问的服务。

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 作为 Azure 堆栈操作员，你[创建计划](azure-stack-create-plan.md)为你的用户提供。 反过来，你的用户订阅您提供要使用的计划和它们包括的服务。 在创建计划时，请确保以设置配额，定义基的计划，以及应考虑包含可选的外接程序计划。

### <a name="quotas"></a>配额

若要帮助你管理你的云容量，你选择，或为计划中创建每个服务的配额。 配额定义的用户订阅可以设置或使用的上部资源限制。 例如，配额可能允许用户创建最多五个虚拟机。 配额可以限制将各种资源，如虚拟机、 RAM 和 CPU 限制。

可以按区域配置配额。 例如，包含从区域 A 的计算服务的规划可能有的两个虚拟机、 4 GB RAM 和 10 个 CPU 内核的配额。 在 Azure 堆栈开发工具包中，只有一个区域 (名为*本地*) 可用。

### <a name="base-plan"></a>基本计划

在创建某项服务，服务管理员可以包括基本计划。 用户订阅该产品时，默认情况下包括这些基本计划。 在用户订阅时，他们有权 （具有相应的配额） 这些基本计划中指定的所有资源提供程序。

### <a name="add-on-plans"></a>外接程序计划

你还可包含可选的外接程序计划中某项服务。 默认情况下，订阅中不包含附加计划。 外接程序计划是 （与配额） 在订阅服务器可以将添加到其订阅产品/服务中可用的其他计划。 例如，你可以决定采用服务向客户提供具有一个试用版的有限资源的基本计划和外接程序计划具有更多实质性的资源。

## <a name="offers"></a>产品

提供是组的创建，以便用户可以订阅它们的一个或多个计划。 例如，产品 Alpha 可能包含计划 A 和计划 B，这两个计划分别包含一组计算服务和一组存储与网络服务。 

当你[创建提议](azure-stack-create-offer.md)，必须包含至少一个基本计划，但你还可以创建用户可添加到其订阅的外接程序计划。


## <a name="subscriptions"></a>订阅

订阅，用户可以如何访问您的提供。 如果你的服务提供程序上的 Azure 堆栈操作员，你的用户 （租户） 通过订阅到您的提供购买你的服务。 如果你在组织的 Azure 堆栈运算符，而无需付出所提供的服务可以订阅你的用户 （员工）。 通过提供用户的每个组合是唯一的订阅。 因此，用户可具有多个提供的订阅，但每个订阅适用于一个产品。 计划、 服务和配额仅适用于每个唯一的订阅 – 它们不能订阅之间共享。 用户创建的每个资源都具有一个订阅相关联。


### <a name="default-provider-subscription"></a>默认提供程序订阅

部署 Azure 堆栈开发工具包时，将自动创建默认提供程序订阅。 此订阅可以用于管理 Azure 堆栈，进一步部署资源提供程序，并创建计划，并为用户提供。 安全和授权的原因，它不应运行客户工作负荷和应用程序。 

## <a name="next-steps"></a>后续步骤

[创建计划](azure-stack-create-plan.md)
