---
title: itsme OpenID Connect 与 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何使用 client_secret 用户流策略将 Azure AD B2C authentication 与 itsme OIDC 集成。 itsme 是数字标识应用。 它允许你安全地登录，无需读卡器、密码、双因素身份验证和多个 PIN 代码。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87489509"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>配置 itsme OpenID Connect (OIDC) 与 Azure Active Directory B2C

Itsme 数字标识应用允许你安全地登录，无需读卡器、密码、双因素身份验证或多个 PIN 代码。 Itsme 应用使用已验证的标识提供强大的客户身份验证。 本文介绍如何使用客户端机密用户流策略将 Azure AD B2C authentication 与 itsme OpenID Connect (OIDC) 集成。

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。
* Itsme 提供的客户端 ID （也称为合作伙伴代码）。
* Itsme 提供的服务代码。
* 你的 itsme 帐户的客户端密码。

## <a name="scenario-description"></a>方案描述

![itsme 体系结构图](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| 步骤 | 说明 |
|------|------|
|1     | 在网站或应用程序中，通过在 Azure AD B2C 用户流中进行调整来包括 " **使用 Itsme 登录** " 按钮。 当用户单击此按钮时，将启动交互流。  |
|2     | Azure AD B2C 通过向 itsme 客户端密码 API 发送授权请求来启动 OpenID connect 流。 众所周知的/OpenID 配置终结点包含有关端点的信息。  |
|3     | Itsme 环境将用户重定向到 "itsme 标识" 页，允许用户填写其电话号码。  |
|4     | Itsme 环境接收来自用户的电话号码并验证正确性。  |
|5     | 如果电话号码属于活动的 itsme 用户，则会为 itsme 应用程序创建一个操作。  |
|6     | 用户打开 itsme 应用程序，检查请求，并确认该操作。  |
|7     |  应用通知 itsme 环境操作已得到确认。 |
|8     |  Itsme 环境返回 Azure AD B2C 的 OAuth 授权代码。 |
|9     |  使用授权代码，Azure AD B2C 执行令牌请求。 |
| 10 | Itsme 环境检查令牌请求，如果仍然有效，则返回 OAuth 访问令牌以及包含请求的用户信息的 ID 令牌。 |
| 11 | 最后，将用户重定向到作为经过身份验证的用户的重定向 url。  |
|   |   |

## <a name="onboard-with-itsme"></a>内置 itsme

1. 若要使用 itsme 创建帐户，请访问 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)上的 itsme。

2. 通过向发送电子邮件来激活你的 itsme 帐户 onboarding@itsme.be 。 你将收到你的 B2C 设置所需的 **合作伙伴代码** 和 **服务代码** 。

3. 激活你的 itsme 合作伙伴帐户后，你将收到一封电子邮件，其中包含指向 **客户端密钥**的一次性链接。

4. 按照 [itsme](https://business.itsme.be/en) 中的说明完成配置。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>在 Azure AD B2C 中设置新的标识提供者

> [!NOTE]
> 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。

1. 确保你正在使用包含 Azure AD B2C 租户的目录。 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。

2. 在 " **Azure 服务**" 下，选择 " **Azure AD B2C** (或选择" **更多服务** "，并使用" **所有服务** "搜索框来搜索 *Azure AD B2C*) 。

3. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。

4. 在窗体中填写以下信息：

   |属性 | 值 |
   |------------ |------- |
   | 名称 | itsme |
   | 元数据 URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>其中 `<environment>` `e2e` (测试环境) 或 `prd` (生产)   |
   | ClientID     | 你的**客户端 ID**（也称为**合作伙伴代码**）  |
   | 客户端机密 | 你的 **client_secret** |
   | 范围  | openid service： YOURSERVICECODE profile email [phone] [address]  |
   |响应类型 | code |
   |响应模式 | query |
   |域提示 | *可以将此空* |
   |UserID | sub |
   |显示名称 | name |
   |给定名称 | given_name |
   |Surname | family_name |
   |电子邮件 | 电子邮件|

5. 选择“保存”。

### <a name="configure-a-user-flow"></a>配置用户流

1. 在 Azure AD B2C 租户中的 " **策略**" 下，选择 " **用户流**"。

2. 选择 " **新建用户流**"。

3. 选择 " **注册并登录**"，选择一个版本，然后选择 " **创建**"。

4. 输入“名称”。

5. 在 " **标识提供者** " 部分中，选择 " **itsme**"。

6. 选择“创建”  。

7. 通过选择用户流名称打开新创建的用户流。

8. 选择 " **属性** "，并调整以下值：

   * 更改 **访问 & ID 令牌生存期 (分钟) ** 为 **5**。
   * 更改**刷新令牌的滑动窗口生存期****不到过期**时间。

### <a name="register-an-application"></a>注册应用程序

1. 在 B2C 租户中的 "**管理**" 下，选择 "**应用注册**  >  **新注册**"。

2. 提供应用程序的 **名称** ，然后输入 **重定向 URI**。 出于测试目的，请输入 `https://jwt.ms` 。

3. 请确保 **已禁用**多重身份验证。

4. 选择“注册”。

   a. 对于测试目的，选择 " **身份验证**"，然后在 " **隐式授予**" 下，选中 " **访问令牌** " 和 " **ID 令牌** " 复选框。  

   b. 选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

1. 在 B2C 租户中，在 " **策略** " 下选择 " **用户流**"。

2. 选择以前创建的用户流。

3. 选择“运行用户流”。

   a. **应用程序**： *选择已注册的应用*

   b. **回复 url**： *选择 "重定向 url"*

4. 此时将显示 itsme **标识自己** 的页面。  

5. 输入你的移动电话号码，然后选择 " **发送**"。

6. 确认 itsme 应用中的操作。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

* [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

* [Azure AD B2C 中的自定义策略入门](custom-policy-get-started.md?tabs=applications)