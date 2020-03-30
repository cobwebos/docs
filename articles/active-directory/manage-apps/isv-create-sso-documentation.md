---
title: 创建&发布应用程序的单一登录文档
description: 独立软件供应商与 Azure 活动目录集成指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232283"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>为应用程序创建和发布单一登录文档   

## <a name="documentation-on-your-site"></a>网站上的文档

易于采用是企业软件决策的重要因素。 清晰的易于遵循的文档可支持客户的采用过程，并降低支持成本。 通过与成千上万的软件供应商合作，微软已经看到了哪些是有效的。

我们建议您在网站上的文档至少包含以下项目。

* SSO 功能简介

  * 支持的协议

  * 版本和 SKU

  * 支持的身份提供程序列表，包含文档链接

* 应用程序的许可信息

* 用于配置 SSO 的基于角色的访问控制

* SSO 配置步骤

  * SAML 的 UI 配置元素，具有来自提供程序的预期值

  * 要传递给标识提供程序的服务提供商信息

* 如果 OIDC/OAuth

  * 具有业务理由同意所需的权限列表

* 试验用户的测试步骤

* 故障排除信息，包括错误代码和消息

* 支持客户机制

## <a name="documentation-on-the-microsoft-site"></a>微软网站上的文档

当您使用 Azure 活动目录应用程序库（在 Azure 应用商店中发布应用程序）列出应用程序时，Microsoft 将为我们共同的客户生成文档，解释分步过程。 你可以[在这里](https://aka.ms/appstutorial)看到一个例子。 本文档是根据您提交到库而创建的，如果您使用 GitHub 帐户对应用程序进行更改，则可以轻松地更新它。

## <a name="next-steps"></a>后续步骤

[在 Azure AD 应用程序库中列出应用程序](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)