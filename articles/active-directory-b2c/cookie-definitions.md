---
title: 쿠키 정의
titleSuffix: Azure AD B2C
description: 为 Azure Active Directory B2C 中使用的 cookie 提供定义。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 399b63cab2594610260997f8e5ecef9c3c05318f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712821"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C 的 cookie 定义

以下各节提供了有关 Azure Active Directory B2C （Azure AD B2C）中使用的 cookie 的信息。

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C 服务与 SameSite 浏览器配置兼容，其中包括支持 `Secure` 特性 `SameSite=None`。

为了保护对站点的访问，web 浏览器将引入一个新的默认的安全模型，该模型假定所有 cookie 都应受到保护，除非另外指定了外部访问权限。 Chrome 浏览器是第一个实现此更改的第一个，从[2 月2020的 Chrome 80](https://www.chromium.org/updates/same-site)开始。 若要详细了解如何在 Chrome 中进行更改，请参阅[开发人员： New SameSite 的准备工作 = 无;](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)Chromium 博客上的安全 Cookie 设置。

开发人员必须使用新的 cookie 设置 `SameSite=None`来指定跨站点访问的 cookie。 存在 `SameSite=None` 属性时，必须使用其他 `Secure` 属性，以便只能通过 HTTPS 连接访问跨站点 cookie。 验证并测试所有应用程序，包括那些使用 Azure AD B2C 的应用程序。

有关详细信息，请参阅[Chrome 版本80或更高版本中的客户网站和 Microsoft 服务和产品的影响](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)。

## <a name="cookies"></a>쿠키

下表列出了 Azure AD B2C 中使用的 cookie。

| 이름 | 도메인 | 만료 | 용도 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [浏览器会话](session-behavior.md)结束 | 跨租户保存用户成员身份数据。 用户是其成员的租户和成员资格级别（管理员或用户）。 |
| `x-ms-cpim-slice` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于将请求路由到相应的生产实例。 |
| `x-ms-cpim-trans` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于跟踪事务（对 Azure AD B2C 的身份验证请求数）和当前事务。 |
| `x-ms-cpim-sso:{Id}` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于维护 SSO 会话。 |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束，身份验证成功 | 用于维护请求状态。 |
| `x-ms-cpim-csrf` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于 CRSF 保护的跨站点请求伪造令牌。 |
| `x-ms-cpim-dc` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于 Azure AD B2C 网络路由。 |
| `x-ms-cpim-ctx` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | Context |
| `x-ms-cpim-rp` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储资源提供程序租户的成员身份数据。 |
| `x-ms-cpim-rc` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储中继 cookie。 |
