---
title: Azure Active Directory B2C 中的用户流版本 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中可用的用户流版本。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d7e174245755659494dfe1243c39619ae37f0f33
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840071"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户流版本

Azure Active Directory B2C （Azure AD B2C）中的用户流可帮助你设置完全描述客户身份体验的常见[策略](user-flow-overview.md)。 这些体验包括注册、登录、密码重置和配置文件编辑。 在 Azure AD B2C 中，可从推荐用户流和预览用户流的集合中进行选择。

新用户流将作为新版本添加。 随着用户流变得稳定，将建议使用这些用户流。 如果用户流已经过全面测试，则会将其标记为“推荐”。 在标记为“推荐”之前，均将用户流视为处于预览状态。 对任何生产应用程序使用推荐的用户流，但从其他版本中选择以在可用时测试新功能。 不应使用旧版本的推荐用户流。

>[!IMPORTANT]
> 除非按**建议**确定用户流，否则它将被视为处于*预览阶段*。 只应为生产应用程序使用建议的用户流。

## <a name="v1"></a>V1

| 用户流 | 推荐 | Description |
| --------- | ----------- | ----------- |
| 密码重置 | 是 | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 配置文件编辑 | 是 | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 使用 ROPC 登录 | 否 | 允许具有本地帐户的用户直接在本机应用程序中登录（无需浏览器）。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li></ul> |
| 登录 | 否 | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>阻止登录</li><li>强制执行密码重置</li><li>使我保持登录状态 (KMSI)</ul><br>无法使用此用户流自定义用户界面。 |
| 注册 | 否 | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 注册和登录 | 是 | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>1\.1 版

| 用户流 | 推荐 | Description |
| --------- | ----------- | ----------- |
| 密码重置1.1 版 | 否 | 允许用户在验证其电子邮件后选择新密码（可用的新页面布局）。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 用户流 | 推荐 | Description |
| --------- | ----------- | ----------- |
| 密码重置 v2 | 否 | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 配置文件编辑 v2 | 是 | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 登录 v2 | 否 | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>登录页自定义</li></ul> |
| 注册 v2 | 否 | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 注册和登录 v2 | 否 | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
