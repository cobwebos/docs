---
title: Azure Stack 计划、产品/服务、配额和订阅概述 | Microsoft Docs
description: 向云操作员介绍 Azure Stack 计划、产品/服务、配额和订阅。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: d8aef778807d3a8a61cf9eedaae24abce84a19ab
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248752"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>计划、产品/服务、配额和订阅概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在 [Azure Stack](azure-stack-poc.md) 中可以交付多种多样的服务，例如虚拟机、SQL Server 数据库、SharePoint、Exchange，甚至 [Azure Marketplace 项](azure-stack-marketplace-azure-items.md)。 Azure Stack 操作员可以使用计划、产品/服务和配额，在 Azure Stack 中配置并交付此类服务。

产品/服务包含一个或多个计划，每个计划包含一个或多个服务。 通过创建计划并将它们组合成不同产品/服务，你可以管理：

- 哪些服务和你的用户可以访问的资源。
- 用户可以使用的资源量。
- 哪些区域有权访问资源。

交付服务时，需遵循以下概要步骤：

1. 添加要交付给用户的服务。
2. 创建了一个或多个服务计划。 创建计划时，需选择或创建配额，用于定义计划中每个服务的资源限制。
3. 创建产品包含一个或多个计划。 产品/服务可以包含基本计划和可选的外接程序计划。

在创建产品/服务后，你的用户可以订阅它来访问服务提供的服务和资源。 用户可以根据需要订阅任意数量的产品/服务。 下图显示了某个用户订阅两个产品/服务的简单示例。 每个产品/服务包含一个或两个计划，每个计划可让用户访问服务。

![租户订阅产品/服务和计划](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 Azure Stack 操作员可以[创建计划](azure-stack-create-plan.md)并将其提供给用户。 反过来，用户可以订阅产品/服务，以便使用其所包括的计划和服务。 创建计划时，请务必设置配额、定义基本计划，并考虑包含可选的附加计划。

### <a name="quotas"></a>配额

若要帮助你管理你的云容量，你可以使用预配置的配额或为计划中创建新的配额为每个服务。 配额定义用户订阅可以部署或使用的资源上限。 例如，配额可能允许用户创建最多五个虚拟机 (Vm)。 在虚拟机，如 RAM 和 CPU 内核上设置附加的配额。

你可以配置按区域的配额。 例如，提供区域 A 的计算服务的计划可能有两个 Vm 使用 4 GB RAM 和 8 个 CPU 内核的配额。

>[!NOTE]
>在 Azure Stack 开发工具包中，只有一个区域（名为 *local*）可用。

详细了解 [Azure Stack 中的配额类型](azure-stack-quota-types.md)。

### <a name="base-plan"></a>基本计划

创建产品/服务时，服务管理员可以包含基本计划。 当用户订阅该产品/服务时，默认会包括这些基本计划。 在用户订阅时，他们有权 （具有相应的配额。） 这些基本计划中指定的所有资源提供程序

### <a name="add-on-plans"></a>加载项计划

附加计划是添加到产品/服务的可选计划。 外接程序计划未包含默认情况下，在订阅中。 产品/服务中提供的附加计划属于额外的计划（附带配额），订户可将其添加订阅中。 例如，可以提供具有有限资源的基本计划供试用，并为确定采用服务的客户提供具有更多实质资源的附加计划。

## <a name="offers"></a>产品

产品/服务是创建的一个或多个计划的组，使用户能够订阅这些产品/服务。 例如，提供字母可以包含计划 A，提供了一套计算服务和计划 B，后者提供了一套存储和网络服务。

[创建产品/服务](azure-stack-create-offer.md)时，必须至少包含一个基本计划，但也可以创建用户可添加到其订阅中的附加计划。

## <a name="subscriptions"></a>订阅

订阅是用户访问产品/服务的方式。 如果你的服务提供程序的 Azure 堆栈运算符，你的用户 （租户） 通过订阅到您的提供购买你的服务。 如果你是组织的 Azure Stack 操作员，则用户（员工）可以订阅你提供的服务，而无需付费。

用户与产品/服务的每种组合都是一个唯一的订阅。 用户可具有多个提供的订阅，但每个订阅仅适用于一个产品。 计划、 服务和配额仅适用于唯一订阅 – 它们不能订阅之间共享。 用户创建的每个资源都与一个订阅相关联。

### <a name="default-provider-subscription"></a>默认提供商订阅

部署 Azure Stack 开发工具包时，系统会自动创建默认提供商订阅。 此订阅可以用于管理 Azure 堆栈，部署其他资源提供程序，并创建计划，并为用户提供。 安全和授权的原因，它不应用于运行客户工作负荷和应用程序。

## <a name="next-steps"></a>后续步骤

[创建计划](azure-stack-create-plan.md)
