---
title: Azure Active Directory B2B 协作的自助注册门户 | Microsoft Docs
description: Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259492"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 协作注册的自助服务门户

客户可借助通过 [Azure 门户](https://portal.azure.com)和面向最终用户的[应用程序访问面板](https://myapps.microsoft.com)公开的内置功能执行许多操作。 但你可能需要为 B2B 用户自定义载入工作流，以适应你组织的需求。 你可以使用[邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 执行该操作。

作为邀请方组织，你可能事先不知道需要访问你的资源的各个外部协作者是谁。 你需要为合作伙伴公司的用户提供一种方式，让你自行注册一套由邀请方组织控制的策略。 通过 API 有可能实现此方案。 [GitHub 上的一个示例项目](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)就是这么做的。

此 GitHub 项目演示了组织如何使用 API 为你信任的合作伙伴提供基于策略的自助注册功能，而且包含确定他们可访问哪些应用的规则。 合作伙伴用户可以在需要时获取对资源的访问权限。 他们可安全地执行此操作，而无需邀请方组织手动将其载入。 可以轻松将项目部署到所选的 Azure 订阅中。

## <a name="as-is-code"></a>原样代码

此代码可作为一个示例，用于演示 Azure Active Directory B2B 邀请 API 的用法。 它应由开发团队或合作伙伴自定义，并且应在将其部署在生产方案中之前对其进行审查。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [Azure AD B2B 协作授权](licensing-guidance.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](faq.md)