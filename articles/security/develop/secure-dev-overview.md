---
title: Microsoft Azure 上的安全开发最佳做法
description: 帮助你开发更安全的代码并在云中部署更安全的应用程序的最佳做法。
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
ms.openlocfilehash: 4df8ff8abfeb7a6ba96ec3344407e95e0a9a3b3d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728719"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure 上的安全开发最佳做法
此系列文章介绍了在为云开发应用程序时要考虑的安全活动和控件。 本文介绍了 Microsoft 安全开发生命周期 (SDL) 的各个阶段, 以及在生命周期的每个阶段要考虑的安全问题和概念。 其目标是帮助你定义可在生命周期的每个阶段中使用的活动和 Azure 服务, 以便设计、开发和部署更安全的应用程序。

本文中的建议来自我们的 Azure 安全经验和客户体验。 您可以使用这些文章作为开发项目的特定阶段中应考虑的事项的参考, 但我们建议您还应通读一次所有文章。 阅读所有文章介绍了你可能在项目的早期阶段丢失的概念。 在发布产品之前实现这些概念可帮助你构建安全的软件、满足安全合规性要求以及降低开发成本。

这些文章旨在作为软件设计人员、开发人员和测试人员的资源, 同时构建和部署安全的 Azure 应用程序。

## <a name="overview"></a>概述

安全性是任何应用程序的最重要方面之一, 但这并不是一件简单的事情。 幸运的是, Azure 提供了许多可帮助你在云中保护你的应用程序的服务。 这些文章介绍了可在软件开发生命周期的每个阶段实现的活动和 Azure 服务, 以帮助你开发更安全的代码, 并在云中部署更安全的应用程序。

## <a name="security-development-lifecycle"></a>安全开发生命周期

遵循安全软件开发的最佳做法要求将安全性集成到软件开发生命周期的每个阶段, 从要求分析到维护, 而不考虑项目方法 ([瀑布](https://en.wikipedia.org/wiki/Waterfall_model)、 [agile](https://en.wikipedia.org/wiki/Agile_software_development)或[DevOps](https://en.wikipedia.org/wiki/DevOps))。 在对高配置数据的破坏和利用操作安全缺陷进行唤醒时, 更多的开发人员了解在整个开发过程中需要解决安全性问题。

稍后在开发生命周期中解决问题, 修复的问题越多。 安全问题不例外。 如果你忽略软件开发早期阶段中的安全问题, 下面的每个阶段可能会继承前面阶段的漏洞。 最终产品会积累多个安全问题, 并可能会遭到破坏。 将安全性构建到开发生命周期的每个阶段可帮助你及早发现问题, 并有助于降低开发成本。

我们按照 Microsoft[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的各个阶段介绍了可用于在生命周期的每个阶段中实现安全软件开发实践的活动和 Azure 服务。

SDL 阶段为:

  - 培训
  - 要求
  - 设计
  - 实现
  - 验证
  - 发行版本
  - 响应

![安全开发生命周期](./media/secure-dev-overview/01-sdl-phase.png)

在这些文章中, 我们将 SDL 阶段分组为 "设计"、"开发" 和 "部署"。

## <a name="engage-your-organizations-security-team"></a>与你组织的安全团队合作

你的组织可能有一个正式的应用程序安全计划, 可帮助你在开发生命周期内从开始到完成安全活动。 如果你的组织具有安全和合规性团队, 请确保在开始开发应用程序之前对其进行参与。 不管是否有任何任务丢失, 都可以在 SDL 的每个阶段询问他们。

我们了解很多读者可能没有要进行的安全或合规性团队。 这些文章可帮助指导你在 SDL 的每个阶段需要考虑的安全问题和决策。

## <a name="resources"></a>资源

使用以下资源来了解有关开发安全应用程序的详细信息, 并帮助保护 Azure 上的应用程序:

[Microsoft 安全开发生命周期 (sdl)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) -SDL 是 microsoft 提供的软件开发流程, 可帮助开发人员构建更安全的软件。 它有助于满足安全遵从性要求, 同时降低开发成本。

[开放式 Web 应用程序安全项目 (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP 是一个在线社区, 可在 Web 应用程序安全性领域生成免费提供的文章、方法、文档、工具和技术。

[向左推送, 如老板](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)–一系列在线文章, 其中概述了开发人员为了创建更安全的代码而应完成的不同类型的应用程序安全活动。

[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)– microsoft 标识平台是 Azure AD 标识服务和开发人员平台的演变。 它是一个功能齐全的平台, 其中包括身份验证服务、开源库、应用程序注册和配置、完整的开发人员文档、代码示例和其他开发人员内容。 Microsoft 标识平台支持行业标准协议, 如 OAuth 2.0 和 OpenID Connect。

[Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)-使用 azure 设计、部署和管理云解决方案时要使用的一系列安全最佳做法。 本文旨在作为 IT 专业人员的资源。 这可能包括构建和部署安全的 Azure 解决方案的设计人员、架构师、开发者和测试人员。

[Azure 上的安全性和符合性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)-azure 安全性和符合性蓝图是可帮助你构建和启动符合严格法规和标准的云驱动应用程序的资源。

## <a name="next-steps"></a>后续步骤
在下面的文章中, 我们建议安全控制和活动, 它们可帮助你设计、开发和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [开发安全的应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
