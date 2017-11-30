---
title: "从 Azure 访问控制服务 (ACS) 迁移"
description: "将应用和服务从 Azure 访问控制服务迁移的方法 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>从 Azure 访问控制服务 (ACS) 迁移

Microsoft Azure Active Directory 访问控制（亦称为“访问控制服务 (ACS)”）将于 2018 年 11 月停用。  目前使用 ACS 的应用和服务必须在此日期之前完全迁移到其他身份验证机制。 本文档为打算弃用 ACS 的当前客户提供了建议。 如果当前未使用 ACS，无需采取任何措施。


## <a name="brief-acs-overview"></a>ACS 简要概述

ACS 是一项云身份验证服务，不仅可以轻松验证和授权用户，获取对 Web 应用和服务的访问权限，同时无需编写代码来实现许多身份验证和授权功能。 它是 .NET 客户端、ASP.NET Web 应用和 WCF Web 服务的开发者和架构师主要使用的服务。

ACS 用例主要可以分为三大类：

- 验证特定 Microsoft 云服务，包括 Azure 服务总线、Dynamics CRM 等。 客户端应用可以从 ACS 获取令牌，用来验证这些服务，从而执行各种操作。
- 向 Web 应用添加身份验证，实现自定义生成服务多样性和预打包服务多样性（如 Sharepoint）。 使用 ACS“被动”身份验证，Web 应用可以支持使用 Google、Facebook、Yahoo、Microsoft 帐户 (Live ID)、Azure Active Directory 和 ADFS 帐户进行登录。
- 使用 ACS 颁发的令牌保护自定义生成 Web 服务。 使用“主动”身份验证，Web 服务可以确保只允许已通过 ACS 验证的已知客户端进行访问。

下面各部分介绍了每个用例及其推荐迁移策略。 在绝大多数情况下，必须大量更改代码，才能将现有应用和服务迁移到最新技术。 建议立即开始计划和执行迁移，以避免由此可能带来的任何故障或故障时间。

> [!WARNING]
> 在大多数情况下，必须大量更改代码，才能将现有应用和服务迁移到最新技术。 建议立即开始计划和执行迁移，以避免由此可能带来的任何故障或故障时间。

在体系结构方面，ACS 完全由以下组件组成：

- 安全令牌服务 (STS)：用于接收身份验证请求，并颁发令牌作为响应返回。
- 经典 Azure 门户：用于创建、删除和启用/禁用 ACS 命名空间。
- 独立 ACS 管理门户：用于自定义和配置 ACS 命名空间的行为。
- 管理服务：可用于自动执行门户功能。
- 令牌转换规则引擎：可用于定义复杂逻辑，控制 ACS 颁发的令牌的输出格式。

必须创建一个或多个 ACS 命名空间，才能使用这些组件。 命名空间是应用和服务与之通信的 ACS 专用实例；它与其他所有使用自己命名空间的 ACS 客户隔离开来。 ACS 命名空间使用专用 URL，如下所示：

```HTTP
https://mynamespace.accesscontrol.windows.net
```

与 STS 的所有通信和管理操作都是通过此 URL 完成，路径因用途而异。 若要确定应用或服务是否使用 ACS，请监视流至 `https://{namespace}.accesscontrol.windows.net` 的任何流量。  流至此 URL 的任何流量都是由 ACS 进行处理，需要停止使用。  不过有一种例外情况，即是流至 `https://accounts.accesscontrol.windows.net` 的任何流量。流至此 URL 的流量已由其他服务进行处理，不受 ACS 弃用影响。  还应确保登录的是 Azure 经典门户，并在拥有的订阅中检查是否有任何 ACS 命名空间。  ACS 命名空间列于“Active Directory”服务的“访问控制命名空间”选项卡下。

若要详细了解 ACS，请参阅 [MSDN 上存档的这篇文档](https://msdn.microsoft.com/library/hh147631.aspx)。

## <a name="retirement-schedule"></a>停用计划

自 2017 年 11 月起，完全支持并可使用所有 ACS 组件。 唯一的限制是，[无法通过经典 Azure 门户新建 ACS 命名空间](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)。

这些组件的弃用时间线如以下计划所示：

- **2017 年 11 月**：经典 Azure 门户中的 Azure AD 管理体验[已停用](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)。 届时，将通过下列新专用 URL 管理 ACS 命名空间：`http://manage.windowsazure.com?restoreClassic=true`。 这样，不仅可以查看、启用/禁用现有命名空间，还能将它们完全删除（如果愿意的话）。
- **2018 年 4 月**：将无法再通过此专用 URL 管理 ACS 命名空间。 届时，将无法禁用/启用、删除或枚举 ACS 命名空间。 不过，ACS 管理门户的功能齐全，具体位于 `https://{namespace}.accesscontrol.windows.net`。 ACS 的其他所有组件也会继续正常运行。
- **2018 年 11 月**：所有 ACS 组件都将永久关闭。 这包括 ACS 管理门户、管理服务、STS 和令牌转换规则引擎。 届时，将无法向 ACS（具体位于 `{namespace}.accesscontrol.windows.net`）发送任何请求。 应在此期限之前将所有现有应用和服务迁移到其他技术。


## <a name="migration-strategies"></a>迁移策略

下面各部分简要概述了从 ACS 迁移到其他 Microsoft 技术的相关建议。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 云服务客户端

所有接受 ACS 颁发的令牌的 Microsoft 云服务现在都支持至少一种备用身份验证。 由于正确的身份验证机制因各个服务而异，因此建议参考各个服务的专属文档，以获取官方指导。 为方便起见，下面列出了各组文档：

| 服务 | 指南 |
| ------- | -------- |
| Azure 服务总线 | [迁移到共享访问签名](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure 中继 | [迁移到共享访问签名](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure 缓存 | [迁移到 Azure Redis 缓存](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure 数据市场 | [迁移到认知服务 API](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk 服务 | [迁移到 Azure 逻辑应用](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure 媒体服务 | [迁移到 Azure AD 身份验证](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure 备份 | [升级 Azure 备份代理](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>使用被动身份验证的 Web 应用

对于使用 ACS 进行用户身份验证的 Web 应用，ACS 向 Web 应用的开发者和架构师提供以下功能：

- 与 Windows Identity Foundation (WIF) 深度集成。
- 与 Google、Facebook、Yahoo、Microsoft 帐户 (Live ID)、Azure Active Directory 和 ADFS 联合。
- 支持以下身份验证协议：OAuth 2.0 草案 13、Ws-Trust 和 Ws-Federation。
- 支持以下令牌格式：JSON Web 令牌 (JWT)、SAML 1.1、SAML 2.0 和简单 Web 令牌 (SWT)。
- WIF 中集成了主页领域发现体验，方便用户选取用于登录的帐户类型。 这种体验由 Web 应用托管，可完全自定义。
- 令牌转换，方便对 Web 应用从 ACS 收到的声明进行丰富自定义，具体包括：
    - 传递标识提供者提供的声明
    - 添加其他自定义声明
    - 用于在特定情况下颁发声明的简单 if-then 逻辑

遗憾的是，尚无一项服务可以等效提供所有这些功能。  应先评估所需的 ACS 功能，再选择使用 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)、[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)，还是使用其他云身份验证服务。

#### <a name="migrating-to-azure-active-directory"></a>迁移到 Azure Active Directory

可以考虑的一种途径是将应用和服务直接与 Azure Active Directory 集成。 Azure AD 是基于云的标识提供者，适用于 Microsoft 工作和学校帐户，同时也是 Office 365、Azure 等的标识提供者。 它提供与 ACS 类似的联合身份验证功能，但并不支持所有 ACS 功能。 一个主要的例子是与社交标识提供者（如 Facebook、Google 和 Yahoo）联合。 如果用户使用这些类型的凭据进行登录，那么 Azure AD 并不适用。 虽然 ACS 和 Azure AD 都支持 OAuth（举个例子），但 Azure AD 不一定支持与 ACS 完全相同的身份验证协议。由于每个实现之间都存在细微差别，因此必须在迁移过程中修改代码。

不过，Azure AD 确实为 ACS 客户带来了多项潜在优势。 它原生支持云中托管的 Microsoft 工作和学校帐户，而 ACS 客户常常使用这些帐户。  Azure AD 租户还可以通过 ADFS 联合到一个或多个本地 Active Directory 实例，这样应用就可以同时验证基于云的用户和本地托管用户。  它还支持 Ws-Federation 协议，这样可以相对简单地使用 Windows Identity Foundation (WIF) 与 Web 应用进行集成。

下表比较了与 Web 应用相关的 ACS 功能与 Azure AD 功能。 粗略来看，如果只允许用户使用 Microsoft 工作和学校帐户进行登录，那么正确选择可能是迁移到 Azure Active Directory。

| 功能 | ACS 支持 | Azure AD 支持 |
| ---------- | ----------- | ---------------- |
| **帐户类型** | | |
| Microsoft 工作和学校帐户 | 支持 | 支持 |
| Windows Server Active Directory 和 ADFS 帐户 | 支持（通过与 Azure AD 租户联合） <br /> 支持（通过与 ADFS 直接联合） | 支持（只通过与 Azure AD 租户联合） | 
| 其他企业标识管理系统帐户 | 支持（通过与 Azure AD 租户联合） <br />支持（通过直接联合） | 支持（通过与 Azure AD 租户联合） |
| 供个人使用的 Microsoft 帐户 (Windows Live ID) | 支持 | 支持（只通过 Azure AD v2.0 OAuth 协议）。 | 
| Facebook、Google、Yahoo 帐户 | 支持 | 都不支持。 |
| **协议和 SDK 兼容性** | | |
| Windows Identity Foundation (WIF) | 支持 | 支持，但有有限说明。 |
| Ws-Federation | 支持 | 支持 |
| OAuth 2.0 | 支持草案 13 | 支持最新规范 RFC 6749。 |
| Ws-Trust | 支持 | 不支持 |
| **令牌格式** | | |
| JSON Web 令牌 (JWT) | 支持 beta 版本 | 支持 |
| SAML 1.1 令牌 | 支持 | 支持 |
| SAML 2.0 令牌 | 支持 | 支持 |
| 简单 Web 令牌 (SWT) | 支持 | 不支持 |
| **自定义** | | |
| 可自定义的主页领域发现/帐户选取 UI | 可以合并到应用的可下载代码 | 不支持 |
| 上传自定义令牌签名证书 | 支持 | 支持 |
| 自定义令牌中的声明 | 传递标识提供者提供的输入声明<br />以声明形式获取标识提供者提供的访问令牌<br />根据输入声明值颁发输出声明<br />颁发含常量值的输出声明 | 无法传递联合标识提供者提供的声明<br />无法以声明形式获取标识提供者提供的访问令牌<br />无法根据输入声明值颁发输出声明<br />可以颁发含常量值的输出声明<br />可以根据同步到 Azure AD 的用户属性颁发输出声明 |
| **自动化** | | |
| 自动执行配置/管理任务 | 支持（通过 ACS 管理服务） | 支持（通过 Microsoft Graph 和 Azure AD 图形 API）。 |

如果决定将应用和服务迁移到 Azure AD 是正确途径，应注意将应用与 Azure AD 集成的方法有两种。

若要使用 Ws-Federation/WIF 与 Azure AD 集成，建议按照[这篇文章中介绍的方法](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)操作。 虽然这篇文章介绍了如何为 Azure AD 配置基于 SAML 的单一登录，但同样也适用于配置 Ws-Federation。 采用这种方法需要使用 Azure AD Premium 许可证，但有以下两项优势：

- 可以完全灵活地自定义 Azure AD 令牌。 这样，可以将 Azure AD 颁发的声明自定义为与 ACS 颁发的声明一致，特别是添加用户 ID 或名称标识符声明。 需要确保 Azure AD 和 ACS 颁发的用户 ID 一致，这样即使在更换技术后，仍可以继续为用户接收一致的用户标识符。
- 可以配置应用专属令牌签名证书，生存期能够自己控制。

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> 采用这种方法需要使用 Azure AD Premium 许可证。 如果是 ACS 客户，并且需要使用高级许可证为应用设置单一登录，请与我们联系，我们将非常乐意提供开发者许可证以供使用。

另一种方法是遵循[此代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)，其中有关如何设置 Ws-Federation 的说明略有不同。 此代码示例不使用 WIF，而是使用 ASP.NET 4.5 OWIN 中间件。 不过，应用注册说明也适用于使用 WIF 的应用，但不需要使用 Azure AD Premium 许可证。  如果采用这种方法，需要了解 [Azure AD 中的签名密钥滚动更新](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 这种方法使用 Azure AD 全局签名密钥来颁发令牌。 默认情况下，WIF 不会自动刷新签名密钥。 如果 Azure AD 轮播全局签名密钥，WIF 实现需要做好接受更改的准备。

如果能够通过 OpenID Connect 或 OAuth 协议与 Azure AD 集成，建议这样做。  [Azure AD 开发者指南](http://aka.ms/aaddev)中收录了大量有关如何将 Azure AD 集成到 Web 应用的文档和指南。

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>迁移到 Azure AD B2C

可以考虑的另一种途径是迁移到 Azure AD B2C。  B2C 是类似于 ACS 的云身份验证服务，可方便开发者将身份验证和标识管理逻辑外包到云服务中。  它是一项专为面向使用者的应用（最多有数百万名活跃用户）设计的付费服务（包含免费层和付费层）。

类似于 ACS，B2C 最具吸引力的功能之一是，支持许多不同类型的帐户。 通过 B2C，可以支持用户使用 Facebook、Google、Microsoft、LinkedIn、GitHub、Yahoo 等帐户进行登录。 此外，B2C 还支持用户专门为应用创建的“本地帐户”/用户名和密码。 Azure AD B2C 还提供丰富扩展性，可用于自定义登录流。 不过，它不支持 ACS 客户可能要求的各种身份验证协议和令牌格式。 它也不能用于从标识提供者、Microsoft 或其他地方获取令牌和查询附加用户信息。

下表比较了与 Web 应用相关的 ACS 功能与 Azure AD B2C 功能。 粗略来看，如果应用面向使用者或支持许多不同类型的帐户，那么正确选择可能是迁移到 Azure AD B2C。

| 功能 | ACS 支持 | Azure AD B2C 支持 |
| ---------- | ----------- | ---------------- |
| **帐户类型** | | |
| Microsoft 工作和学校帐户 | 支持 | 支持（通过自定义策略）  |
| Windows Server Active Directory 和 ADFS 帐户 | 支持（通过与 ADFS 直接联合） | 支持（通过使用自定义策略的 SAML 联合身份验证） |
| 其他企业标识管理系统帐户 | 支持（通过使用 Ws-Federation 直接联合） | 支持（通过使用自定义策略的 SAML 联合身份验证） |
| 供个人使用的 Microsoft 帐户 (Windows Live ID) | 支持 | 支持 | 
| Facebook、Google、Yahoo 帐户 | 支持 | 原生支持 Facebook 和 Google，通过使用自定义策略的 OpenID Connect 联合身份验证支持 Yahoo |
| **协议和 SDK 兼容性** | | |
| Windows Identity Foundation (WIF) | 支持 | 不支持。 |
| Ws-Federation | 支持 | 不支持。 |
| OAuth 2.0 | 支持草案 13 | 支持最新规范 RFC 6749。 |
| Ws-Trust | 支持 | 不支持。 |
| **令牌格式** | | |
| JSON Web 令牌 (JWT) | 支持 beta 版本 | 支持 |
| SAML 1.1 令牌 | 支持 | 不支持 |
| SAML 2.0 令牌 | 支持 | 不支持 |
| 简单 Web 令牌 (SWT) | 支持 | 不支持 |
| **自定义** | | |
| 可自定义的主页领域发现/帐户选取 UI | 可以合并到应用的可下载代码 | 完全可自定义 UI（通过自定义 CSS）。 |
| 上传自定义令牌签名证书 | 支持 | 自定义签名密钥（而不是证书），支持（通过自定义策略）。 |
| 自定义令牌中的声明 | 传递标识提供者提供的输入声明<br />以声明形式获取标识提供者提供的访问令牌<br />根据输入声明值颁发输出声明<br />颁发含常量值的输出声明 | 可以传递标识提供者提供的声明。 一些声明要求有自定义策略。<br />无法以声明形式获取标识提供者提供的访问令牌<br />可以通过自定义策略根据输入声明值颁发输出声明<br />可以通过自定义策略颁发含常量值的输出声明 |
| **自动化** | | |
| 自动执行配置/管理任务 | 支持（通过 ACS 管理服务） | 允许通过 Azure AD 图形 API 创建用户。 无法以编程方式创建 B2C 租户、应用或策略。 |

如果决定将应用和服务迁移到 Azure AD B2C 是正确途径，应从以下资源着手：

- [Azure AD B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>其他迁移选项

如果 Azure AD 和 Azure AD B2C 都无法满足 Web 应用的需求，请与我们联系，让我们帮助你确定最佳迁移途径。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>使用主动身份验证的 Web 服务

对于受 ACS 颁发的令牌保护的 Web 服务，ACS 提供以下功能：

- 在可用于请求获取令牌的 ACS 命名空间中注册一个或多个服务标识。
- 支持使用以下类型凭据，通过 OAuth WRAP 和 OAuth 2.0 草案 13 协议请求获取令牌：
    - 为服务标识创建的简单密码
    - 使用对称密钥或 X509 证书的签名 SWT
    - 受信任的标识提供者（通常为 ADFS 实例）颁发的 SAML 令牌
- 支持以下令牌格式：JSON Web 令牌 (JWT)、SAML 1.1、SAML 2.0 和简单 Web 令牌 (SWT)。
- 简单令牌转换规则

ACS 中的服务标识通常用于实现服务器间 (S2S) 身份验证。  

#### <a name="migrating-to-azure-active-directory"></a>迁移到 Azure Active Directory

对于这种类型的身份验证流，建议迁移到 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)。 Azure AD 是基于云的标识提供者，适用于 Microsoft 工作和学校帐户，同时也是 Office 365、Azure 等的标识提供者。 不过，它也可用于服务器间身份验证（使用 Azure AD 实现的 OAuth 客户端凭据授予）。  下表比较了服务器间身份验证中的 ACS 功能与 Azure AD 功能。

| 功能 | ACS 支持 | Azure AD 支持 |
| ---------- | ----------- | ---------------- |
| 如何注册 Web 服务 | 在 ACS 管理门户中创建信赖方。 | 在 Azure 门户中创建 Azure AD Web 应用。 |
| 如何注册客户端 | 在 ACS 管理门户中创建服务标识。 | 在 Azure 门户中创建另一个 Azure AD Web 应用。 |
| 使用的协议 | OAuth WRAP 协议<br />OAuth 2.0 草案 13 客户端凭据授予 | OAuth 2.0 客户端凭据授予 |
| 客户端身份验证方法 | 简单密码<br />签名 SWT<br />联合 IDP 提供的 SAML 令牌 | 简单密码<br />签名 JWT |
| 令牌格式 | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | 仅 JWT |
| 令牌转换 | 添加自定义声明<br />简单 if-then 声明颁发逻辑 | 添加自定义声明 | 
| 自动执行配置/管理任务 | 支持（通过 ACS 管理服务） | 支持（通过 Microsoft Graph 和 Azure AD 图形 API）。 |

若要了解如何实现服务器间方案，请参阅以下资源：

- [Azure AD 开发者指南的服务间部分](https://aka.ms/aaddev)。
- [使用简单密码客户端凭据的守护程序代码示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [使用证书客户端凭据的守护程序代码示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>其他迁移选项

如果 Azure AD 无法满足 Web 服务的需求，请发表评论，让我们帮助你确定最适合用例的迁移计划。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>问题、疑虑和反馈

我们了解，在阅读本文后，许多 ACS 客户都确定不了明确的迁移途径，在确定正确计划方面可能需要获得一些协助或指导。 若要讨论迁移方案和提问，请在此页上发表评论。
