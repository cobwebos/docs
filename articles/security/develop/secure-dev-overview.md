---
title: Microsoft Azure 上的安全开发最佳做法
description: 帮助你开发更安全的代码以及在云中部署更安全的应用程序的最佳做法。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "68934867"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure 上的安全开发最佳做法
此系列文章介绍了在为云开发应用程序时要考虑的安全活动和控制措施。 其中还说明了 Microsoft 安全开发生命周期 (SDL) 的各个阶段及生命周期各阶段中要考虑的安全问题和概念。 目标是帮助你定义可在生命周期的每个阶段中使用的活动和 Azure 服务，以便设计、开发和部署更安全的应用程序。

这些文章中提出的建议来自于我们在 Azure 安全性方面的经验以及我们客户的经验。 可以使用这些文章作为参考，来了解在开发项目的某个特定阶段应考虑哪些事项，但我们还是建议你至少从头到尾通读一遍所有的文章。 阅读所有文章可让你了解在项目早期阶段可能会忽视的概念。 在发布产品之前落实这些概念，可帮助你构建安全的软件、满足安全合规性要求并降低开发成本。

构建和部署安全的 Azure 应用程序，会涉及专业程度各不相同的软件设计人员、开发人员和测试人员，这些文章旨在为他们提供参考资源。

## <a name="overview"></a>概述

安全性是任何应用程序的最重要方面之一，但这并不是一件简单的事情。 幸运的是，Azure 提供了许多服务，可帮助你保护云中的应用程序。 这些文章介绍了可在软件开发生命周期各阶段实施的活动和 Azure 服务，以帮助你开发更安全的代码以及在云中部署更安全的应用程序。

## <a name="security-development-lifecycle"></a>安全开发生命周期

遵循安全软件开发的最佳做法要求将安全性集成到软件开发生命周期的每个阶段，从要求分析到维护，而不考虑项目方法 ([瀑布](https://en.wikipedia.org/wiki/Waterfall_model)性、 [agile](https://en.wikipedia.org/wiki/Agile_software_development)或 [DevOps](https://en.wikipedia.org/wiki/DevOps)) 。 随着引入关注的数据泄露和运营方面的安全缺陷被利用等情况的发生，越来越多的开发人员理解了解决安全性问题是需要贯穿整个开发过程的。

在开发生命周期中，解决问题的时间越晚，那么解决问题要耗费的成本也就越高。 安全性问题也不例外。 如果在软件开发早期阶段漠视安全问题，那么后面的每个阶段可能会继承前面的阶段中存在的漏洞。 最终产品将会积累很多安全问题，还可能会发生数据泄露。 将安全性融入开发生命周期的每个阶段，会有助于及早发现问题，并且有助于降低开发成本。

我们按照 Microsoft [安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 的各个阶段来介绍活动和 Azure 服务，你可以利用它们在生命周期的各个阶段中完成安全软件开发实践。

SDL 各阶段包括：

  - 培训
  - 要求
  - 设计
  - 实现
  - 验证
  - 发布
  - 响应

![安全开发生命周期](./media/secure-dev-overview/01-sdl-phase.png)

在这些文章中，我们将 SDL 的各个阶段划分为三类：设计、开发和部署。

## <a name="engage-your-organizations-security-team"></a>与你组织的安全团队合作

你的组织可能有正式的应用程序安全计划，用于在开发生命周期过程中自始至终为你提供安全活动方面的帮助。 如果你的组织有安全性和合规性团队，那么在开始开发应用程序前一定要请他们一起参与进来。 在 SDL 的每个阶段都要询问他们，是否存在一些被你忽视的任务。

我们理解，许多读者可能没有可以合作的安全性或合规性团队。 这些文章有助于为你在 SDL 各阶段需要考虑的安全性问题和决策提供指导。

## <a name="resources"></a>资源

以下资源可用于了解有关开发安全应用程序的详细信息，以及帮助保护 Azure 上的应用程序：

[Microsoft 安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL 是源于 Microsoft 的一种软件开发过程，可帮助开发人员构建更加安全的软件。 它有助于在满足安全性合规要求的同时降低开发成本。

[开放式 Web 应用程序安全性项目 (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP 是一个在线社区，它提供 Web 应用程序安全性领域的免费文章、方法、文档、工具和技术。

[向左推送，如老板](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) –一系列在线文章，其中概述了开发人员为了创建更安全的代码而应完成的不同类型的应用程序安全活动。

[Microsoft 标识平台](../../active-directory/develop/index.yml) – Microsoft 标识平台是由 Azure AD 标识服务和开发人员平台演变而来。 它是一个全功能平台，包含身份验证服务、开源库、应用程序注册和配置、完整的开发人员文档、代理示例，以及其他开发人员内容。 Microsoft 标识平台支持 OAuth 2.0 和 OpenID Connect 这样的行业标准协议。

[Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) -使用 azure 设计、部署和管理云解决方案时要使用的一系列安全最佳做法。 本文旨在作为 IT 专业人员的资源。 这可能包括构建和部署安全的 Azure 解决方案的设计人员、架构师、开发者和测试人员。

[Azure 安全性与合规性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) - Azure 安全性与合规性蓝图资源有助于构建和推出符合严格法规和标准的云助力应用程序。

## <a name="next-steps"></a>后续步骤
下面的文章中推荐了一些安全控制措施和活动，有助于设计、开发和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [开发安全的应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
