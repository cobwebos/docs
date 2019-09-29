---
title: 设置通过 OpenID Connect 注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C 设置通过 OpenID Connect 注册与登录。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0303f8c7e18a5c229bc5a8c5e9b90d95cdaccbe7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672916"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 OpenID Connect 注册与登录

[OpenID Connect](active-directory-b2c-reference-oidc.md) 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于安全的用户登录。 Azure AD B2C 支持大多数使用此协议的标识提供者。 本文介绍如何将自定义 OpenID Connect 标识提供者添加到用户流。

## <a name="add-the-identity-provider"></a>添加标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是：单击顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”，然后选择“新建 OpenID Connect 提供程序”。

## <a name="configure-the-identity-provider"></a>配置标识提供者

每个 OpenID Connect 标识提供者都描述一个元数据文档，该文档包含了登录所需的大部分信息。 这包括要使用的 URL 和服务的公共签名密钥的位置等信息。 OpenID Connect 元数据文档始终位于以 `.well-known\openid-configuration` 结尾的的终结点上。 输入想要添加的 OpenID Connect 标识提供者的元数据 URL。

## <a name="client-id-and-secret"></a>客户端 ID 和密码

为了允许用户登录，标识提供者将要求开发人员在其服务中注册应用程序。 此应用程序具备 ID（也就是“客户端 ID”）和客户端密码。 从标识提供者中复制这些值，并将其输入到相应的字段中。

> [!NOTE]
> 客户端密码是可选项。 但是，如果想使用[授权代码流](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)（它使用客户端机密来交换令牌代码），则必须输入该机密。

## <a name="scope"></a>范围

范围定义你希望从自定义标识提供者收集的信息和权限。 OpenID Connect 请求必须包含 `openid` 范围值，以便从标识提供者接收 ID 令牌。 如果没有 ID 令牌，用户就无法使用自定义标识提供者登录到 Azure AD B2C。 可以追加其他范围（以空格分隔）。 请参阅自定义标识提供者的文档，查看其他可用范围。

## <a name="response-type"></a>响应类型

响应类型描述在首次调用自定义标识提供者的 `authorization_endpoint` 时发回的信息类型。 可以使用以下响应类型：

* `code`：根据[授权代码流](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)，会将一个代码返回到 Azure AD B2C。 Azure AD B2C 将继续调用 `token_endpoint`，以交换令牌代码。
* `id_token`：ID 令牌将从自定义标识提供者返回到 Azure AD B2C。

## <a name="response-mode"></a>响应模式

响应模式定义将数据从自定义标识提供者发送回 Azure AD B2C 时采用的方法。 可以使用以下响应模式：

* `form_post`：建议使用此响应模式以获得最佳安全性。 响应通过 HTTP `POST` 方法传输，并使用 `application/x-www-form-urlencoded` 格式将代码或令牌编码在正文中。
* `query`：代码或令牌将作为查询参数返回。

## <a name="domain-hint"></a>域提示

域提示可用于直接跳转至指定标识提供者的登录页面，用户无需在可用标识提供者列表中进行选择。 若要允许这类行为，请输入域提示值。 若要跳转至自定义标识提供者，请在调用 Azure AD B2C 进行登录时将参数 `domain_hint=<domain hint value>` 追加至请求末尾。

## <a name="claims-mapping"></a>声明映射

在自定义标识提供者将 ID 令牌发送回 Azure AD B2C 之后，Azure AD B2C 需将接收到的令牌中的声明映射到 Azure AD B2C 识别并使用的声明。 对于以下每个映射，请参阅自定义标识提供者的文档，以了解标识提供者的令牌中返回的声明：

* **用户 ID**：输入提供已登录用户唯一标识符的声明。
* **显示名称**：输入提供用户显示名称或全名的声明。
* **名字**：输入提供用户名字的声明。
* **姓氏**：输入提供用户姓氏的声明。
* **电子邮件**：输入提供用户电子邮件地址的声明。
