---
title: "使用自定义策略管理 SSO 会话 - Azure AD B2C | Microsoft Docs"
description: "了解如何使用 Azure AD B2C 中的自定义策略管理 SSO 会话。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 809f6000-2e52-43e4-995d-089d85747e1f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: parja
ms.openlocfilehash: ff767993eaf0305168176d0ad6e15c068b8c85eb
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C：单一登录 (SSO) 会话管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在用户已完成身份验证后，Azure AD B2C 可让管理员控制 Azure AD B2C 与用户交互的方式。 这是通过 SSO 会话管理实现的。 例如，管理员可以控制是否显示所选的标识提供者，或是否需要再次输入本地帐户详细信息。 本文介绍如何配置 Azure AD B2C SSO 的设置。

## <a name="overview"></a>概述

SSO 会话管理包括两个部分。 第一个部分处理用户与 Azure AD B2C 之间的直接交互，另一个部分处理用户与外部参与方（例如 Facebook）之间的交互。 Azure AD B2C 不会重写或绕过外部参与方可能保留的 SSO 会话。 通过 Azure AD B2C 转到外部参与方的路由将被“记住”，因此无需重新提示用户选择其社交或企业标识提供者。 最终的 SSO 决策仍由外部参与方做出。

## <a name="how-does-it-work"></a>工作原理

SSO 会话管理使用的语义，与自定义策略中其他任何技术配置文件使用的语义相同。 执行某个业务流程步骤时，会在与该步骤关联的技术配置文件中查询 `UseTechnicalProfileForSessionManagement` 引用。 如果存在该引用，则会检查引用的 SSO 会话提供程序，确定用户是否为会话参与者。 如果是，则使用 SSO 会话提供程序来重新填充会话。 同样，在完成执行某个业务流程步骤后，如果已指定 SSO 会话提供程序，则使用该提供程序将信息存储在会话中。

Azure AD B2C 定义了大量可用的 SSO 会话提供程序：

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO 管理类是使用技术配置文件的 `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` 元素指定的。

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

顾名思义，此提供程序不执行任何操作。 此提供程序可用于抑制特定技术配置文件的 SSO 行为。

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

此提供程序可以用于在会话中存储声明。 此提供程序通常在用于管理本地帐户的技术配置文件中引用。 

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

若要在会话中添加声明，可使用技术配置文件的 `<PersistedClaims>` 元素。 使用提供程序重新填充会话时，持久保存的声明会添加到声明包。 `<OutputClaims>` 用于从会话检索声明。

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

此提供程序用于抑制“选择标识提供者”屏幕。 它通常在针对外部标识提供者（例如 Facebook）配置的技术配置文件中引用。 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

此提供程序用于管理应用与外部 SAML 标识提供者之间的 Azure AD B2C SAML 会话。

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

技术配置文件中有两个元数据项：

| 项目 | 默认值 | 可能的值 | 说明
| --- | --- | --- | --- |
| IncludeSessionIndex | 是 | true/false | 向提供程序指出应存储会话索引。 |
| RegisterServiceProviders | 是 | true/false | 指示提供程序应注册已颁发断言的所有 SAML 服务提供程序。 |

使用提供程序存储 SAML 标识提供者会话时，上述项应该均为 false。 使用提供程序存储 B2C SAML 会话时，上述项应为 true 或被省略，因为默认值为 true。

>[!NOTE]
> 需要 `SessionIndex` 和 `NameID` 才能完成 SAML 会话注销。

## <a name="next-steps"></a>后续步骤

我们期望获得反馈和建议！ 如果在本主题中遇到任何困难，请使用标记[“azure-ad-b2c”](https://stackoverflow.com/questions/tagged/azure-ad-b2c)在 Stack Overflow 上提问。 另外，请在[反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)中为功能请求投票。

