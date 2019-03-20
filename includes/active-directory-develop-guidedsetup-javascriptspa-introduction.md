---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a6fcbc0e8adac75f17d7379ff512ba650d0bb118
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203284"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>从 JavaScript 单页应用程序 (SPA) 调用 Microsoft Graph API

本指南演示 JavaScript 单页应用程序 (SPA) 如何从 Azure Active Directory v2.0 终结点登录个人、工作和学校帐户，获取访问令牌以及调用 Microsoft Graph API 或其他需要访问令牌的 API。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![演示如何按本教程适用生成示例应用](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro-updated.png)

<!--start-collapse-->
### <a name="more-information"></a>更多信息

本指南创建的示例应用程序允许 JavaScript SPA 查询接受 Azure Active Directory v2.0 终结点令牌的 Microsoft Graph API 或 Web API。 此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>库

本指南使用以下库：

|库|描述|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|适用于 JavaScript 的 Microsoft 身份验证库预览|

> [!NOTE]
> msal.js 面向 Azure Active Directory v2.0 终结点，这使得个人、学校和工作帐户能够登录和获取令牌。 Azure Active Directory v2.0 终结点具有[某些限制](../articles/active-directory/develop/active-directory-v2-limitations.md)。
> 若要了解 v1.0 和 v2.0 终结点之间的差异，请参阅[终结点比较指南](../articles/active-directory/develop/azure-ad-endpoint-comparison.md)。

<!--end-collapse-->
