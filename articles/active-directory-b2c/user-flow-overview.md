---
title: Azure Active Directory B2C 中的用户流 | Microsoft Docs
titleSuffix: Azure AD B2C
description: 详细了解 Azure Active Directory B2C 的可扩展策略框架以及如何创建各种用户流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a7736602fafb740d1d76fa09fd26da25e4ff9f5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481591"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户流

为帮助您为应用程序设置最常见的标识任务，Azure AD B2C 门户包括称为**用户流**的预定义的可配置策略。 用户流允许你确定用户进行登录、注册、编辑配置文件或重置密码等操作时，用户与应用程序进行交互的方式。 借助用户流，你可以控制以下功能：

- 用于登录的帐户类型，例如 Facebook 等社交帐户，或本地帐户
- 从使用者收集的属性，例如名字、邮政编码和鞋码
- Azure 多重身份验证
- 用户界面的自定义
- 应用程序接收的信息（令牌中的声明）

可以在租户中创建多个不同类型的用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。 由于这种灵活性，只需对代码做出极少量的更改或根本不需要更改，即可定义和修改标识体验。 应用程序使用包含用户流参数的标准 HTTP 身份验证请求来触发用户流。 接收自定义[令牌](tokens-overview.md)作为响应。

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

Azure AD B2C 包括几种类型的用户流：

- **注册和登录** - 通过一项配置处理注册和登录体验。 根据上下文将用户引导至正确的路径。 还包含单独的**注册**或**登录**用户流。 但是，我们通常建议组合注册和登录用户流。
- **个人资料编辑** - 让用户编辑其个人资料信息。
- **密码重置** - 用于配置是否允许用户重置其密码以及如何重置密码。

大多数用户流类型都有**推荐**的版本和**标准**版本。 有关详细信息，请参阅[用户流版本](user-flow-versions.md)。

> [!IMPORTANT]
> 如果你之前在 Azure AD B2C 中处理过用户流，你会注意到我们已更改引用用户流版本的方式。 以前，我们提供了 V1 （生产就绪）版本、1.1 版和 V2 （预览版）。 现在，我们已将用户流合并为两个版本：
>
>- **建议**用户流是用户流的新预览版本。 它们经过全面测试，并结合了旧版**V2**和**1.1**版本的所有功能。 今后，将维护并更新新的建议用户流。 转到这些新的建议用户流后，就可以在新功能发布后对其进行访问。
>- **标准**用户流（以前称为**V1**）通常是可用的、生产就绪的用户流。 如果你的用户流非常关键并且依赖于高度稳定的版本，你可以继续使用标准用户流，这会认识到这些版本不会进行维护和更新。
>
>所有旧版预览版用户流（1.1 版和 V2）都在**2021 年8月1日**之前的路径上过时。 我们强烈建议您尽可能快[地切换到新的**推荐**用户流](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow)，以便您始终可以利用最新的功能和更新。

## <a name="linking-user-flows"></a>链接用户流

使用本地帐户的**注册或登录**用户流在体验的第一个页面上包含“忘记了密码?”链接****。 单击此链接不会自动触发密码重置用户流。

而是将错误代码 `AADB2C90118` 返回给应用程序。 应用程序需要通过运行一个可重置密码的特定用户流来处理此错误代码。 有关示例，请查看演示用户流链接方法的[简单 ASP.NET 示例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

## <a name="email-address-storage"></a>电子邮件地址存储

用户流中可能需要电子邮件地址。 如果用户使用社交标识提供者进行身份验证，电子邮件地址将存储在 **otherMails** 属性中。 如果本地帐户基于用户名，则电子邮件地址将存储在强身份验证详细信息属性中。 如果本地帐户基于电子邮件地址，则电子邮件地址将存储在 **signInNames** 属性中。

不保证在所有这些情况下都会验证电子邮件地址。 租户管理员可以在本地帐户的基本策略中禁用电子邮件验证。 即使启用了电子邮件地址验证，但如果地址来自社交标识提供者并且尚未更改，则也不会验证地址。

只有**user.othermails**和**signInNames**属性通过 Microsoft Graph API 公开。 强身份验证详细信息属性中的电子邮件地址不可用。

## <a name="next-steps"></a>后续步骤

若要创建建议的用户流，请按照[教程：创建用户流](tutorial-create-user-flows.md)中的说明进行操作。
