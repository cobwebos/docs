---
title: 适用于 Microsoft 标识平台的最佳做法 | Azure
description: 了解在与 Microsoft 标识平台集成时的最佳做法、建议和最容易疏忽的问题。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: c7c26780cdba37a2bddc201928dbcbd5f86e0170
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115891"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft 标识平台最佳做法和建议

本文重点说明在与 Microsoft 标识平台集成时的最佳做法、建议和最容易疏忽的问题。  此查检表可引导你完成高质量且安全的集成。 请定期查看此列表，确保维持应用与标识平台之间的集成的质量和安全性。 请不要参照该查检表来评审整个应用程序。 在我们不断改进平台的过程中，该查检表的内容将会更改。

如果你刚刚入门，请查看 [Microsoft 标识平台文档](index.yml)来了解身份验证基础知识、Microsoft 标识平台中的应用方案，等等。

使用以下查检表确保应用程序与 [Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)有效集成。

> [!TIP]
> Azure 门户中的 "*集成助手*" 可帮助你应用其中许多最佳实践和建议。 在 Azure 门户中选择任意[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)，然后选择 "**集成助手（预览版）** " 菜单项以开始助手。

## <a name="basics"></a>基础知识

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 阅读并理解 [Microsoft 平台策略](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)。 确保应用程序符合旨在保护用户和平台的所述条款。 |

## <a name="ownership"></a>所有权

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 确保用来注册和管理应用的、与帐户关联的信息是最新的。 |

## <a name="branding"></a>品牌

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 遵守[适用于应用程序的品牌准则](howto-add-branding-in-azure-ad-apps.md)。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 为应用程序提供有意义的名称和徽标。 此信息将显示在[应用程序的许可提示](application-consent-experience.md)中。 确保提供的名称和徽标能够代表公司/产品的形象，以帮助用户做出明智的决定。 确保不要违反任何商标法案。 |

## <a name="privacy"></a>隐私

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 提供应用服务条款和隐私声明的链接。 |

## <a name="security"></a>安全性

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 管理重定向 URI： <ul><li>保留所有重定向 URI 的所有权，并使其 DNS 记录保持最新。</li><li>不要在 URI 中使用通配符 (*)。</li><li>对于 Web 应用，请确保所有 URI 安全且已经过加密（例如，使用 https 方案）。</li><li>对于公共客户端，在适用的情况下请使用特定于平台的重定向 URI（主要适用于 iOS 和 Android）。 否则，请使用包含大量随机内容的重定向 URI，以防在回调应用时发生冲突。</li><li>如果在隔离的 Web 代理中使用应用，则可以使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。</li><li>定期审查并修剪所有未使用或不必要的重定向 URI。</li></ul> |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果应用已注册到目录中，请最小化并手动监视应用注册所有者的列表。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 除非有明确的要求，否则请不要启用对 [OAuth2 隐式授权流](v2-oauth2-implicit-grant-flow.md)的支持。 在[此处](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)了解有效方案。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 不要只是使用用户名/密码。 不要使用[资源所有者密码凭据流 (ROPC)](v2-oauth-ropc.md)，因为它会直接处理用户的密码。 此流所需的信任度和用户公开度很高，仅当无法使用其他更安全的流时，才应使用此流。 在某些方案（比如 DevOps）中仍需要此流，但请注意，使用该流将对应用程序施加约束。  有关其他新式方法，请阅读[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 保护和管理 Web 应用、Web API 和守护程序应用的机密应用凭据。 使用[证书凭据](active-directory-certificate-credentials.md)，而不是密码凭据（客户端机密）。 如果必须使用密码凭据，请不要手动设置。 不要将凭据存储在代码或配置中，切勿允许人类处理这些凭据。 如果可能，请使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)或 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 存储和定期轮换凭据。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 确保应用程序请求最低特权权限。 只在有需要时，才请求应用程序绝对需要的权限。 了解不同的[权限类型](v2-permissions-and-consent.md#permission-types)。 仅在必要时使用应用程序权限；尽量使用委托的权限。 有关 Microsoft Graph 权限的完整列表，请参阅此[权限参考](https://docs.microsoft.com/graph/permissions-reference)。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果你在使用 Microsoft 标识平台保护 API，请仔细考虑该 API 应该公开的权限。 考虑解决方案需要哪种适当的粒度级，以及哪些权限需要管理员许可。 在做出任何授权决策之前，请检查传入令牌中的预期权限。 |

## <a name="implementation"></a>实现

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用新式身份验证解决方案（OAuth 2.0、[OpenID Connect](v2-protocols-oidc.md)）将用户安全登录。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) |  不要直接对 OAuth 2.0 和 Open ID 等协议进行编程。 请改用 [Microsoft 身份验证库 (MSAL)](msal-overview.md)。 MSAL 库在易于使用的库中安全地包装安全协议，并为[条件访问](/azure/active-directory/conditional-access/overview)方案、设备范围的[单一登录（SSO）](/azure/active-directory/manage-apps/what-is-single-sign-on)和内置令牌缓存支持提供内置支持。 有关详细信息，请参阅 Microsoft 支持的[客户端库](reference-v2-libraries.md#microsoft-supported-client-libraries)和[中间件库](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)列表，以及[兼容的第三方客户端库](reference-v2-libraries.md#compatible-client-libraries)列表。<br/><br/>如果必须为身份验证协议手动编写代码，应遵循 [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx) 等方法。 请认真对待每个协议的标准规范中的安全注意事项。|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) |  将现有应用从 [Azure Active Directory 身份验证库 (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) 迁移到 [Microsoft 身份验证库](msal-overview.md)。 MSAL 是 Microsoft 的最新标识平台解决方案，比 ADAL 更普及。 它适用于 .NET、JavaScript、Android、iOS 和 macOS，并为 Python 和 Java 推出了公共预览版。 详细了解如何迁移 [ADAL.NET](msal-net-migration.md)、[ADAL.js](msal-compare-msal-js-and-adal-js.md) 以及 [ADAL.NET 和 iOS 中介](msal-net-migration-ios-broker.md)应用。|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) |  对于移动应用，请使用应用程序注册体验来配置每个平台。 要使应用程序能够利用 Microsoft Authenticator 或 Microsoft 公司门户进行单一登录，需要为应用配置“中介重定向 URI”。 这样，在身份验证后，Microsoft 就可将控制权递回给应用程序。 配置每个平台时，应用注册体验会引导你完成该过程。 使用快速入门下载工作示例。 在 iOS 上，请尽可能地使用中介和系统 webview。|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) |  在 Web 应用或 Web API 中，为每个帐户保留一个令牌缓存。  对于 Web 应用，令牌缓存应使用帐户 ID 进行键控。  对于 Web API，帐户应使用用于调用该 API 的令牌的哈希值进行键控。 MSAL.NET 在 .NET Framework 和 .NET Core 子平台中提供自定义令牌缓存序列化。 出于安全和性能方面的原因，我们建议为每个用户序列化一个缓存。 有关详细信息，请阅读[令牌缓存序列化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)。|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果应用所需的数据可以通过 [Microsoft Graph](https://developer.microsoft.com/graph) 提供，请使用 Microsoft Graph 终结点而不是单个 API 请求此数据的权限。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) |不要查看访问令牌值，或尝试将其分析为客户端。  此类操作可能会更改值、格式，甚至在不发出警告的情况下将值加密 - 如果客户端需要了解有关用户的某种信息，请始终使用 id_token，或调用 Microsoft Graph。  只能由 Web API 分析访问令牌（因为格式定义和加密密钥设置就是由这些 API 完成的）。 |

## <a name="end-user-experience"></a>最终用户体验

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | [了解许可体验](application-consent-experience.md)并配置应用许可提示的片段，使最终用户和管理员有足够的信息确定是否可信任你的应用。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 在执行交互式流之前尝试进行静默身份验证（静默令牌获取），以尽量减少用户在使用应用时输入登录凭据所需的时间。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 不要对每次登录使用“prompt=consent”。 仅当你已确定需要请求其他权限的许可（例如，如果已更改应用的所需权限）时，才使用 prompt=consent。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果适用，请使用用户数据扩充应用程序。 使用 [Microsoft Graph API](https://developer.microsoft.com/graph) 可以轻松实现此目的。 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)工具可帮助你开始。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 注册应用所需的完整权限集，使管理员能够轻松地向其租户授予许可。 在运行时使用[增量许可](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)，以帮助用户了解应用在首次启动时为何要请求可能会给用户带来忧虑或困惑的权限。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 实现[干净的单一注销体验](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 这是一项隐私和安全要求，有助于建立良好的用户体验。 |

## <a name="testing"></a>测试

|   |   |
|---|---|
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 测试可能影响用户使用应用程序的能力的[条件访问策略](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 |
| ![复选框](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用你计划支持的所有可能的帐户（例如，工作或学校帐户、个人 Microsoft 帐户、子帐户和主权帐户）来测试应用程序。 |

## <a name="additional-resources"></a>其他资源

浏览有关 v2.0 的深入信息：

* [Microsoft 标识平台 (v2.0) 概述](v2-overview.md)
* [Microsoft 标识平台协议参考](active-directory-v2-protocols.md)
* [访问令牌参考](access-tokens.md)
* [ID 令牌参考](id-tokens.md)
* [身份验证库参考](reference-v2-libraries.md)
* [Microsoft 标识平台中的权限和许可](v2-permissions-and-consent.md)
* [Microsoft 图形 API](https://developer.microsoft.com/graph)
