---
title: Cookie 定义-Azure Active Directory B2C |Microsoft Docs
description: 为 Azure Active Directory B2C 中使用的 cookie 提供定义。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930892"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的 cookie 定义

下表列出了 Azure Active Directory B2C 中使用的 cookie。

| 名称 | 域 | 过期时间 | 用途 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | [浏览器会话](session-behavior.md)结束 | 跨租户保存用户成员身份数据。 用户是其成员的租户和成员资格级别（管理员或用户）。 |
| x-ms-cpim-slice | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于将请求路由到相应的生产实例。 |
| x-ms-cpim-trans | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于跟踪事务（对 Azure AD B2C 的身份验证请求数）和当前事务。 |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于维护 SSO 会话。 |
| cpim： {id} _n | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束，身份验证成功 | 用于维护请求状态。 |
| x-ms-cpim-csrf | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于 CRSF 保护的跨站点请求伪造令牌。 |
| x-ms-cpim-dc | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于 Azure AD B2C 网络路由。 |
| x-ms-cpim-ctx | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 上下文 |
| x-ms-cpim-rp | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储资源提供程序租户的成员身份数据。 |
| x-ms-cpim-rc | login.microsoftonline.com、b2clogin.com、署名域 | [浏览器会话](session-behavior.md)结束 | 用于存储中继 cookie。 |
