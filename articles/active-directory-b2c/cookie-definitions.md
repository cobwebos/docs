---
title: Cookie 定义
titleSuffix: Azure AD B2C
description: 为 Azure Active Directory B2C 中使用的 Cookie 提供定义。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189508"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C 的 Cookie 定义

以下各部分提供了有关 Azure Active Directory B2C (Azure AD B2C) 中使用的 Cookie 的信息。

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C 服务与 SameSite 浏览器配置兼容，其中包括支持`SameSite=None`和`Secure`属性。

为了保护对站点的访问，Web 浏览器将引入一个新的“默认保护”模型，除非另行指定，该模型假定所有 Cookie 都应阻止来自外部的访问。 Chrome 浏览器是第一个实现此更改的浏览器，从 [2020 年 2 月份的 Chrome 80 开始](https://www.chromium.org/updates/same-site)。 若要详细了解如何针对 Chrome 中的此更改做好准备，请参阅 Chromium 博客上的[开发人员：针对新的 SameSite=None 做好准备；安全的 Cookie 设置](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)。

开发人员必须使用新的 Cookie 设置 `SameSite=None` 来指定用于跨站点访问的 Cookie。 当存在 `SameSite=None` 属性时，必须使用一个额外的 `Secure` 属性，以便使跨站点 Cookie 只能通过 HTTPS 连接进行访问。 验证并测试你的所有应用程序，包括那些使用 Azure AD B2C 的应用程序。

有关详细信息，请参阅：

* [在 Chrome 浏览器中处理 SameSite Cookie 更改](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Chrome 80 或更高版本对客户网站、Microsoft 服务和产品的影响](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie

下表列出了 Azure AD B2C 中使用的 Cookie。

| 名称 | Domain | 过期时间 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [浏览器会话](session-behavior.md)结束 | 保存各个租户的用户成员身份数据。 用户所属的租户，以及成员身份级别（管理员或用户）。 |
| `x-ms-cpim-slice` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于将请求路由到相应的生产实例。 |
| `x-ms-cpim-trans` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于跟踪事务（对 Azure AD B2C 发出的身份验证请求数）和当前事务。 |
| `x-ms-cpim-sso:{Id}` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于保留 SSO 会话。 |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束，身份验证成功 | 用于保留请求状态。 |
| `x-ms-cpim-csrf` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于实现 CRSF 保护的跨网站请求伪造令牌。 |
| `x-ms-cpim-dc` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于 Azure AD B2C 网络路由。 |
| `x-ms-cpim-ctx` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 上下文 |
| `x-ms-cpim-rp` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储资源提供程序租户的成员身份数据。 |
| `x-ms-cpim-rc` | b2clogin.com、login.microsoftonline.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储中继 Cookie。 |
