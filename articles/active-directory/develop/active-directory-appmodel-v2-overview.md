---
title: Azure Active Directory v2.0 终结点 | Microsoft Docs
description: 介绍如何使用 Microsoft 帐户和 Azure Active Directory 登录名生成应用程序。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, jmprieur, elisol, dastrock
ms.custom: aaddev
ms.openlocfilehash: 8cf592ddd2c4a68e15856d968b77813604713b78
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592034"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>在单个应用程序中将 Microsoft 帐户和 Azure Active Directory 用户登录
在过去，想要支持个人 Microsoft 帐户和 Azure Active Directory 中的工作帐户的应用程序开发人员必须集成两个单独的系统。 Azure Active Directory (Azure AD) v2.0 终结点推出了新的身份验证 API 版本，可简化此过程。 使用 Azure AD v2.0 终结点可通过一次集成从这两种类型的帐户登录。 使用 Azure AD v2.0 终结点的应用程序还可以通过其中一种帐户从 [Microsoft 图形 API](https://graph.microsoft.io) 使用 REST API。

## <a name="getting-started"></a>入门
从下述列表中选择偏爱的平台，以使用 Microsoft 开源库与框架生成应用程序。 也可以使用 OAuth 2.0 和 OpenID Connect 协议直接发送和接收协议消息，而无需使用身份验证库。
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>详细了解 Azure AD v2.0 终结点
了解 Azure AD v2.0 终结点的作用：

* 探索[可以使用 Azure AD v2.0 终结点生成的应用程序类型](v2-app-types.md)。
* 了解 Azure AD v2.0 终结点的[限制、局限性和约束](active-directory-v2-limitations.md)。
* 观看此视频了解 Azure AD v2.0 终结点的概述：

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>其他资源
浏览有关 Azure AD v2.0 终结点平台的详细信息：

* [Azure AD v2.0 协议参考](active-directory-v2-protocols.md)
* [Azure AD v2.0 令牌参考](v2-id-and-access-tokens.md)
* [Azure AD v2.0 身份验证库参考](reference-v2-libraries.md)
* [Azure AD v2.0 终结点中的范围和许可](v2-permissions-and-consent.md)
* [Microsoft 图形 API](https://graph.microsoft.io)

> [!NOTE]
> 如果只需从 Azure Active Directory 登录到工作和学校帐户，请从 [Azure AD 开发人员指南](azure-ad-developers-guide.md)入手。 Azure AD v2.0 终结点供明确需要登录到 Microsoft 个人帐户的开发人员使用。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
