---
title: 创建 & 发布应用程序的单一登录文档
description: 独立软件供应商与 Azure Active Directory 集成的指南
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232283"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>创建和发布应用程序的单一登录文档   

## <a name="documentation-on-your-site"></a>网站上的文档

易用性是企业软件决策中的一个重要因素。 明确的易于理解的文档在采用旅程中支持客户并降低支持成本。 Microsoft 使用成千上万个软件供应商。

建议你至少在网站上包含以下各项。

* SSO 功能简介

  * 支持的协议

  * 版本和 SKU

  * 支持的标识提供者列表和文档链接

* 应用程序的授权信息

* 用于配置 SSO 的基于角色的访问控制

* SSO 配置步骤

  * 具有提供程序中的预期值的 SAML 的 UI 配置元素

  * 要传递给标识提供程序的服务提供程序信息

* If OIDC/OAuth

  * 同意业务理由所需的权限列表

* 试验用户的测试步骤

* 疑难解答信息，包括错误代码和消息

* 客户的支持机制

## <a name="documentation-on-the-microsoft-site"></a>Microsoft 网站上的文档

当你使用 Azure Active Directory 应用程序库列出你的应用程序（该应用程序也会在 Azure Marketplace 中发布应用程序）时，Microsoft 将为我们的共同客户生成文档，其中介绍了分步过程。 可在[此处](https://aka.ms/appstutorial)查看示例。 此文档是基于你提交到库的创建的，如果你使用 GitHub 帐户对应用程序进行更改，则可以轻松地对其进行更新。

## <a name="next-steps"></a>后续步骤

[在 Azure AD 应用程序库中列出你的应用程序](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)