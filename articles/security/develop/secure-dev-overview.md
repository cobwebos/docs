---
title: 安全开发 Microsoft Azure 的最佳做法
description: 若要帮助您开发更安全代码并将其部署在云中更安全的应用程序的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144459"
---
# <a name="secure-development-best-practices-on-azure"></a>安全开发 Azure 的最佳做法
本系列文章提供了安全活动和用于开发云应用程序时，请考虑控件。 介绍了 Microsoft 安全开发生命周期 (SDL) 和安全问题和概念，可考虑在生命周期的每个阶段的阶段。 旨在帮助您定义活动和可用于在生命周期的每个阶段中设计、 开发和部署更安全的应用程序的 Azure 服务。

文章中的建议都从我们的 Azure 安全经验和我们的客户的体验。 您使用这些文章作为参考的特定阶段的开发项目中，应考虑什么，但我们建议你也阅读所有从开头到末尾至少一次的文章。 阅读所有文章向您介绍您可能已经遗漏了在你的项目的早期阶段中的概念。 在发布产品之前实现这些概念有助于您构建安全软件，满足符合性的安全要求，并降低开发成本。

这些文章旨在为软件设计人员，开发人员的资源，测试人员所有级别的人员生成和部署安全的 Azure 应用程序。

## <a name="overview"></a>概述

安全性是任何应用程序的最重要方面之一，它不是简单的操作才能得到正确结果。 幸运的是，Azure 提供了很多服务，可帮助你保护云中的应用程序。 这些文章地址活动和 Azure 服务可以实现在软件开发生命周期，以帮助您开发更安全代码并将其部署在云中更安全的应用程序的每个阶段。

## <a name="security-development-lifecycle"></a>安全开发生命周期

遵循最佳做法，为安全的软件开发需要将安全集成到软件开发生命周期，从要求到维护，而不考虑项目方法分析的每个阶段 ([瀑布图](https://en.wikipedia.org/wiki/Waterfall_model)，[敏捷](https://en.wikipedia.org/wiki/Agile_software_development)，或[DevOps](https://en.wikipedia.org/wiki/DevOps))。 在高配置文件数据泄漏隐患唤醒和操作安全性缺陷利用，更多开发人员都了解安全需要在整个开发过程中解决。

解决问题的更高版本中您开发的生命周期，数据越多，修复将耗费。 安全问题也不例外。 如果您在软件开发的早期阶段中忽略了安全问题，遵循每个阶段可能会继承在前一阶段的安全漏洞。 最终产品会累积了多个安全问题和漏洞的可能性。 安全性构建到开发生命周期的每个阶段可帮助您尽早发现问题并帮助您降低开发成本。

Microsoft 的阶段，我们将遵循[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)引入活动和可用于满足安全软件开发实践生命周期的每个阶段中的 Azure 服务。

SDL 阶段为：

  - 培训
  - 要求
  - 设计
  - 实现
  - 验证
  - 发行版本
  - 响应

![安全开发生命周期](./media/secure-dev-overview/01-sdl-phase.png)

在这些文章中我们将 SDL 阶段分组设计、 开发和部署。

## <a name="engage-your-organizations-security-team"></a>咨询组织的安全团队

你的组织可能具有协助你完成从开始到结束在开发生命周期的安全活动正式的应用程序安全计划。 如果你的组织有安全性和符合性团队，确保其参与度，在开始开发应用程序之前。 要求他们在 SDL 的每个阶段是否有你错过了任何任务。

我们了解到许多读者可能没有安全性或合规性团队参与。 这些文章可帮助指导你在中的安全问题和需要考虑在 SDL 的每个阶段的决策。

## <a name="resources"></a>资源

若要了解有关开发安全应用程序的详细信息，并有助于保护 Azure 上的应用程序，请使用以下资源：

[Microsoft 安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL 是帮助开发人员构建更安全的软件的 microsoft 软件开发过程。 它可以帮助您降低开发成本的同时解决安全合规性要求。

[打开 Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP 是免费提供的文章、 方法、 文档、 工具和技术的 web 应用程序安全性的字段中将生成一个在线社区。

[将左侧、 推送如老板](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca)– 概述了不同类型的应用程序开发人员应该完成创建更安全代码的安全活动的一系列的在线文章。

[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)– Microsoft 标识平台是一种演变，Azure AD 标识服务和开发人员平台。 它是一个全功能的平台，身份验证服务、 开放源代码库、 应用程序注册和配置、 完整的开发人员文档、 代码示例和其他开发人员内容组成。 Microsoft 标识平台支持 OAuth 2.0 和 OpenID Connect 等行业标准协议。

[Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)– 安全最佳实践来设计、 部署和管理云解决方案通过使用 Azure 时使用的集合。 本白皮书旨在为 IT 专业人员的资源。 这可能包括构建和部署安全的 Azure 解决方案的设计人员、架构师、开发者和测试人员。

[安全和 Azure 上的符合性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview)– Azure 安全性和符合性蓝图是资源，可帮助你构建和启动云为后盾的应用程序符合严格的法规和标准。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议的安全控件和活动，有助于您设计、 开发和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [开发安全应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
