---
title: 在 Microsoft Azure 上保护开发最佳实践
description: 帮助您开发更安全的代码并在云中部署更安全的应用程序的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934867"
---
# <a name="secure-development-best-practices-on-azure"></a>在 Azure 上保护开发最佳实践
本系列文章介绍了为云开发应用程序时需要考虑的安全活动和控制措施。 介绍了 Microsoft 安全开发生命周期 （SDL） 的各个阶段以及生命周期每个阶段要考虑的安全问题和概念。 目标是帮助您定义可在生命周期每个阶段用于设计、开发和部署更安全应用程序的活动和 Azure 服务。

文章中的建议来自我们对 Azure 安全性的体验以及客户的经验。 您可以使用这些文章作为开发项目特定阶段应考虑的参考，但我们建议您至少从头到尾阅读所有文章。 阅读所有文章将介绍您可能在项目早期阶段遗漏的概念。 在发布产品之前实现这些概念可以帮助您构建安全软件、满足安全合规性要求并降低开发成本。

这些文章旨在成为构建和部署安全 Azure 应用程序的所有级别的软件设计人员、开发人员和测试人员的资源。

## <a name="overview"></a>概述

安全性是任何应用程序最重要的方面之一，它不是一件简单的事。 幸运的是，Azure 提供了许多服务，可帮助您在云中保护应用程序。 这些文章解决了可以在软件开发生命周期的每个阶段实现的活动和 Azure 服务，以帮助您开发更安全的代码并在云中部署更安全的应用程序。

## <a name="security-development-lifecycle"></a>安全开发生命周期

遵循安全软件开发的最佳实践需要将安全性集成到软件开发生命周期的每个阶段，从需求分析到维护，而不管项目方法（[瀑布](https://en.wikipedia.org/wiki/Waterfall_model)、[敏捷](https://en.wikipedia.org/wiki/Agile_software_development)或[DevOps）。](https://en.wikipedia.org/wiki/DevOps) 在高调数据泄露和利用操作安全漏洞之后，越来越多的开发人员认识到，在整个开发过程中需要解决安全性问题。

越晚修复开发生命周期中的问题，修复成本就越多。 安全问题也不例外。 如果忽略软件开发早期阶段的安全问题，则以下每个阶段可能会继承前一阶段的漏洞。 您的最终产品将累积多个安全问题和违规的可能性。 将安全性构建到开发生命周期的每个阶段可帮助您及早发现问题，并帮助您降低开发成本。

我们遵循 Microsoft[安全开发生命周期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的各个阶段，介绍可用于在生命周期每个阶段实现安全软件开发实践的活动和 Azure 服务。

SDL 阶段包括：

  - 培训
  - 要求
  - 设计
  - 实现
  - 验证
  - 发布
  - 响应

![安全开发生命周期](./media/secure-dev-overview/01-sdl-phase.png)

在这些文章中，我们将 SDL 阶段分组到设计、开发和部署中。

## <a name="engage-your-organizations-security-team"></a>与组织的安全团队接洽

您的组织可能具有正式的应用程序安全程序，在开发生命周期中从头到尾帮助您开展安全活动。 如果您的组织有安全和合规团队，请务必在开始开发应用程序之前与他们接洽。 在 SDL 的每个阶段询问他们是否有任何任务您遗漏了。

我们理解，许多读者可能没有安全或合规团队参与。 这些文章可以帮助您指导您在 SDL 每个阶段需要考虑的安全问题和决策。

## <a name="resources"></a>资源

使用以下资源了解有关开发安全应用程序的更多信息，并帮助保护 Azure 上的应用程序：

[微软安全开发生命周期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL 是微软的软件开发过程，可帮助开发人员构建更安全的软件。 它可以帮助您满足安全合规性要求，同时降低开发成本。

[开放 Web 应用程序安全项目 （OWASP）](https://www.owasp.org/index.php/Main_Page) – OWASP 是一个在线社区，可在 Web 应用程序安全领域生成免费提供的文章、方法、文档、工具和技术。

[向左推，像老板一样](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)——一系列在线文章，概述了开发人员应完成不同类型的应用程序安全活动，以创建更安全的代码。

[微软标识平台](../../active-directory/develop/index.yml)– 微软标识平台是 Azure AD 标识服务和开发人员平台的演变。 它是一个功能齐全的平台，由身份验证服务、开源库、应用程序注册和配置、完整的开发人员文档、代码示例和其他开发人员内容组成。 Microsoft 标识平台支持行业标准协议，如 OAuth 2.0 和 OpenID 连接。

[Azure 解决方案的安全最佳实践](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)– 使用 Azure 设计、部署和管理云解决方案时要使用的安全最佳实践集合。 本文旨在成为 IT 专业人员的资源。 这可能包括构建和部署安全的 Azure 解决方案的设计人员、架构师、开发者和测试人员。

[Azure 上的安全和合规性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)– Azure 安全和合规性蓝图是可帮助您构建和启动符合严格法规和标准的云支持应用程序的资源。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议安全控制和活动，以帮助您设计、开发和部署安全应用程序。

- [设计安全应用程序](secure-design.md)
- [开发安全应用程序](secure-develop.md)
- [部署安全应用程序](secure-deploy.md)
