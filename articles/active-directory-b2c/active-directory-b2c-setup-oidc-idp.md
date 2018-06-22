---
title: 在 Azure Active Directory B2C 中将 OpenID Connect 标识提供者添加到内置策略 | Microsoft Docs
description: 关于如何在 Azure AD B2C 中将 OpenID Connect 提供者添加到内置策略的概述指南。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e4325a7c87ac9975e819b22536838ec438fa1bcd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709557"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C：将自定义的 OpenID Connect 标识提供者添加到内置策略

>[!NOTE]
> 此功能目前以公共预览版提供。 请勿在生产环境中使用该功能。

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于让用户安全登录。 Azure AD B2C 中支持大多数使用此协议的标识提供者（例如 [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)）。 本文介绍如何将自定义 OpenID Connect 标识提供者添加到内置策略。

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>配置自定义 OpenID Connect 标识提供者

若要添加自定义 OpenID Connect 标识提供者，请完成以下操作：

1. 按照以下步骤在 Azure 门户中[导航到 Azure AD B2C 设置](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
1. 单击“标识提供者”。
1. 单击“添加”。
1. 选择“OpenID Connect”作为“标识提供者类型”。

### <a name="setting-up-the-openid-connect-identity-provider"></a>设置 OpenID Connect 标识提供者

#### <a name="metadata-url"></a>元数据 URL

根据规范，每个 OpenID Connect 标识提供者都描述一个元数据文档，该文档包含了登录所需的大部分信息。 这包括要使用的 URL 和服务的公共签名密钥的位置等信息。 OpenID Connect 元数据文档始终位于以 `.well-known\openid-configuration` 结尾的的终结点上。

输入想要添加的 OpenID Connect 标识提供者的元数据 URL。

#### <a name="client-id-and-secret"></a>客户端 ID 和密码

为了允许用户登录，标识提供者要求开发人员在其服务中注册应用程序。 此应用程序具备 ID（也就是“客户端 ID”）和客户端密码。 从标识提供者中复制这些值，并将其输入到相应的字段中。

> [!NOTE]
> 客户端密码是可选项。 但是，如果想使用[授权代码流](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)（它使用该密码来交换令牌代码），则必须输入客户端密码。

#### <a name="scope"></a>范围

范围定义希望从自定义标识提供者收集的信息和权限。 OpenID Connect 请求必须包含 `openid` 范围值，以便从标识提供者接收 ID 令牌。 如果没有 ID 令牌，用户就无法使用自定义标识提供者登录到 Azure AD B2C.

可以追加其他范围（以空格分隔）。 请参阅自定义标识提供者的文档，查看其他可用范围。

#### <a name="response-type"></a>响应类型

响应类型描述在首次调用自定义标识提供者的 `authorization_endpoint` 时发回的信息类型。 

* `code`：根据[授权代码流](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)，会将一个代码返回到 Azure AD B2C。 然后，Azure AD B2C 将继续调用 `token_endpoint`，交换令牌代码。
* `token`：访问令牌将从自定义标识提供者返回到 Azure AD B2C。
* `id_token`：ID 令牌将从自定义标识提供者返回到 Azure AD B2C。


#### <a name="response-mode"></a>响应模式

响应模式定义将数据从自定义标识提供者发送回 Azure AD B2C 时采用的方法。

* `form_post`：建议使用此响应模式以获得最佳安全性。 响应通过 HTTP `POST` 方法传输，并使用 `application/x-www-form-urlencoded` 格式将代码或令牌编码在正文中。
* `query`：代码或令牌将作为查询参数返回。


#### <a name="domain-hint"></a>域提示

域提示可用于直接跳转至指定标识提供者的登录页面，用户无需在可用标识提供者列表中进行选择。 若要允许这类行为，请输入域提示值。

若要跳转至自定义标识提供者，请在调用 Azure AD B2C 进行登录时将参数 `domain_hint=<domain hint value>` 追加至请求末尾。


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>映射来自 OpenID Connect 标识提供者的声明

在自定义标识提供者将 ID 令牌发送回 Azure AD B2C 之后，Azure AD B2C 需将接收到的令牌中的声明映射到 Azure AD B2C 识别并使用的声明。 

对于以下每个映射，请参阅自定义标识提供者的文档，了解标识提供者的令牌中返回的声明。

* `User ID`：输入提供登录用户唯一标识符的声明。
* `Display Name`：输入提供用户显示名称或全名的声明。
* `Given Name`：输入提供用户名字的声明。
* `Surname`：输入提供用户姓氏的声明。
* `Email`：输入提供用户电子邮件地址的声明。

## <a name="next-steps"></a>后续步骤

将自定义 OpenID Connect 标识提供者添加到[内置策略](active-directory-b2c-reference-policies.md)。
