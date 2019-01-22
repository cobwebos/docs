---
title: Azure Active Directory B2C 中的用户流 | Microsoft Docs
description: 详细了解 Azure Active Directory B2C 的可扩展策略框架以及如何创建各种用户流。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bcbd26c8e78e29daa78a7e50f2f49b095103f696
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351775"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户流

Azure Active Directory (Azure AD) B2C可扩展的策略框架是服务的核心优势。 策略充分描述了标识体验，例如注册、登录或配置文件编辑。 若要帮助设置最常见的标识任务，Azure AD B2C 门户应包括名为“用户流”的预定义且可配置的策略。 

## <a name="what-are-user-flows"></a>什么是用户流？

用户流可让你通过配置以下设置来控制应用程序的行为：

- 用于登录的帐户类型，例如 Facebook 等社交帐户，或本地帐户
- 从使用者收集的属性，例如名字、邮政编码和鞋码
- Azure 多重身份验证
- 用户界面的自定义
- 应用程序接收的信息（令牌中的声明） 

可以在租户中创建多个不同类型的用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。 由于这种灵活性，只需对代码做出极少量的更改或根本不需要更改，即可定义和修改标识体验。 应用程序使用包含用户流参数的标准 HTTP 身份验证请求来触发用户流。 接收自定义[令牌](active-directory-b2c-reference-tokens.md)作为响应。 

以下示例演示了指定要使用的用户流的“p”查询字符串参数：

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>用户流版本

Azure 门户中一直在添加[新版本的用户流](user-flow-versions.md)。 当你开始使用 Azure AD B2C 时，系统会建议使用经过测试的用户流。 创建新用户流时，可从“建议”选项卡中选择所需的用户流。

目前建议使用以下用户流：

- **注册和登录** - 通过一项配置处理注册和登录体验。 根据上下文将用户引导至正确的路径。 建议优先使用此用户流，而不要使用**注册**用户流或**登录**用户流。
- **个人资料编辑** - 让用户编辑其个人资料信息。
- **密码重置** - 用于配置是否允许用户重置其密码以及如何重置密码。

## <a name="linking-user-flows"></a>链接用户流

使用本地帐户的**注册或登录**用户流在体验的第一个页面上包含“忘记了密码?”链接。 单击此链接不会自动触发密码重置用户流。 

而是将错误代码 `AADB2C90118` 返回给应用程序。 应用程序需要通过运行一个可重置密码的特定用户流来处理此错误代码。 有关示例，请查看演示用户流链接方法的[简单 ASP.NET 示例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

## <a name="email-address-storage"></a>电子邮件地址存储

用户流中可能需要电子邮件地址。 如果用户使用社交标识提供者进行身份验证，电子邮件地址将存储在 **otherMails** 属性中。 如果本地帐户基于用户名，则电子邮件地址将存储在强身份验证详细信息属性中。 如果本地帐户基于电子邮件地址，则电子邮件地址将存储在 **signInNames** 属性中。
 
不保证在所有这些情况下都会验证电子邮件地址。 租户管理员可以在本地帐户的基本策略中禁用电子邮件验证。 即使启用了电子邮件地址验证，但如果地址来自社交标识提供者并且尚未更改，则也不会验证地址。
 
只会通过 Active Directory 图形 API 公开 **otherMails** 和 **signInNames** 属性。 强身份验证详细信息属性中的电子邮件地址不可用。

## <a name="next-steps"></a>后续步骤

若要创建建议的用户流，请遵照[教程：创建用户流](tutorial-create-tenant.md)中的说明。


