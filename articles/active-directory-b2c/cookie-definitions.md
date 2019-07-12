---
title: Cookie 定义-Azure Active Directory B2C |Microsoft Docs
description: 提供了有关在 Azure Active Directory B2C 中使用的 cookie 的定义。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: af3244a32e9d02a1ba5053da85547bf614053127
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67587414"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的 cookie 定义

下表列出了在 Azure Active Directory B2C 中使用的 cookie。

| 名称 | Domain | 过期时间 | 用途 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 在租户间保存用户成员身份数据。 一个用户的租户是成员和级别的成员身份 （管理员或用户）。 |
| x-ms-cpim-slice | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 使用将请求路由到适当的生产实例。 |
| x-ms-cpim-trans | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 用于跟踪事务 （到 Azure AD B2C 的身份验证请求数） 和当前事务。 |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 可用来维护 SSO 会话。 |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 最终[浏览器会话](active-directory-b2c-token-session-sso.md)，成功进行身份验证 | 用于维护请求状态。 |
| x-ms-cpim-csrf | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 跨站点请求伪造用于 CRSF 保护令牌。 |
| x-ms-cpim-dc | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 用于 Azure AD B2C 网络路由。 |
| x-ms-cpim-ctx | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 上下文 |
| x-ms-cpim-rp | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 用于成员资格数据存储资源提供程序的租户。 |
| x-ms-cpim-rc | login.microsoftonline.com、 b2clogin.com、 经过品牌打造的域 | 结束的[浏览器会话](active-directory-b2c-token-session-sso.md) | 用于存储中继 cookie。 |

