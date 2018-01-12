---
title: "从 Azure 访问控制服务迁移 | Microsoft Docs"
description: "从 Azure 访问控制服务迁移应用和服务的方法"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: f3de9016fe29a51ab2c7fb9e93fcd33af0f0e871
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="migrate-from-the-azure-access-control-service"></a>从 Azure 访问控制服务迁移

Azure 访问控制（一种 Azure Active Directory (Azure AD) 服务）将于 2018 年 11 月停用。 在那之前，当前使用访问控制的应用程序和服务必须全部完整迁移到其他身份验证机制。 本文为打算弃用访问控制的当前客户提供相关建议。 如果当前未使用访问控制，则无需执行任何操作。


## <a name="overview"></a>概述

访问控制是一种云身份验证服务，它提供了一种简单的方法来验证用户身份和授予用户对 Web 应用程序和服务的访问权限。 它允许将身份验证和授权的诸多功能从代码中分离出来。 Microsoft .NET 客户端、ASP.NET Web 应用程序和 Windows Communication Foundation (WCF) Web 服务的开发人员和架构师是访问控制的主要使用者。

访问控制用例主要可以分为三大类：

- 验证特定 Microsoft 云服务，包括 Azure 服务总线和 Dynamics CRM。 客户端应用程序从访问控制获取令牌，对这些服务进行验证，从而执行各种操作。
- 向 Web 应用程序添加身份验证，包括自定义和预打包的应用程序（如 SharePoint）。 通过使用访问控制“被动”身份验证，Web 应用程序可支持使用 Microsoft 帐户（以前称为 Live ID）以及 Google、Facebook、Yahoo、Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 的帐户登录。
- 使用访问控制颁发的令牌保护自定义 Web 服务。 使用“主动”身份验证，Web 服务可确保只允许已通过访问控制验证的已知客户端进行访问。

下面各部分介绍了每个用例及其推荐迁移策略。 

> [!WARNING]
> 在大多数情况下，必须大量更改代码，才能将现有应用和服务迁移到较新的技术。 建议立即开始计划和执行迁移，以避免出现任何潜在的中断或故障时间。

访问控制包括以下组件：

- 安全令牌服务 (STS)：用于接收身份验证请求并颁发安全令牌作为响应返回。
- Azure 经典门户：可在此创建、删除和启用/禁用访问控制命名空间。
- 单独的访问控制管理门户：可在此自定义和配置访问控制命名空间。
- 管理服务：可用于自动执行门户功能。
- 令牌转换规则引擎：可用于定义复杂逻辑，控制访问控制颁发的令牌的输出格式。

必须创建一个或多个访问控制命名空间，才能使用这些组件。 命名空间是访问控制的一个专用实例，应用程序和服务要与之进行通信。 命名空间独立于所有其他访问控制客户。 其他访问控制客户使用自己的命名空间。 访问控制中的命名空间具有专用 URL，如下所示：

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

与 STS 的所有通信以及管理操作都是通过此 URL 完成。 所用路径因用途而异。 若要确定应用程序或服务是否使用访问控制，可监视抵达 https://\<命名空间\>.accesscontrol.windows.net 的任何通信流。 流至此 URL 的任何流量都是由访问控制进行处理，需停止使用。 

但抵达 https://accounts.accesscontrol.windows.net 的任何通信流除外。 流至此 URL 的流量已由其他服务进行处理，不受访问控制弃用影响。 

还应登录 Azure 经典门户，并在拥有的订阅中检查是否有任何访问控制命名空间。 访问控制命名空间列于“Active Directory”服务下的“访问控制命名空间”选项卡中。

有关访问控制的详细信息，请参阅[访问控制服务 2.0（已存档）](https://msdn.microsoft.com/library/hh147631.aspx)。

## <a name="retirement-schedule"></a>停用计划

自 2017 年 11 月起，完全支持并可使用所有访问控制组件。 唯一的限制是[无法通过 Azure 经典门户创建新的访问控制命名空间](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)。

以下是访问控制组件停用计划：

- **2017 年 11 月**：[停用](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Azure 经典门户中的 Azure AD 管理体验。 此时，可通过新的专用 URL 实现访问控制的命名空间管理：http://manage.windowsazure.com?restoreClassic=true。 如有需要，可使用此 URl 查看现有命名空间、启用/禁用命名空间和删除命名空间。
- **2018 年 4 月**：无法再通过 http://manage.windowsazure.com?restoreClassic=true 专用 URL 进行访问控制命名空间管理。 此时，不能禁用/启用、删除或枚举访问控制命名空间。 但可通过 https://\<命名空间\>.accesscontrol.windows.net 访问功能完善的访问控制管理门户。 访问控制的其他所有组件继续正常运行。
- **2018 年 11 月**：永久关闭所有访问控制组件。 这包括访问控制管理门户、管理服务、STS 和令牌转换规则引擎。 此时，发送到访问控制（位于 \<命名空间\>.accesscontrol.windows.net）的所有请求都会失败。 应在此之前将所有现有应用和服务迁移到其他技术。


## <a name="migration-strategies"></a>迁移策略

下面各部分简要概述了从访问控制迁移到其他 Microsoft 技术的相关建议。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 云服务客户端

所有接受访问控制颁发的令牌的 Microsoft 云服务现在都支持至少一种备用身份验证。 每个服务的身份验证机制各不相同。 建议参阅每个服务的特定文档，获取官方指南。 为方便起见，下面列出了各组文档：

| 服务 | 指南 |
| ------- | -------- |
| Azure 服务总线 | [迁移到共享访问签名](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure 服务总线中继 | [迁移到共享访问签名](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure 托管缓存 | [迁移到 Azure Redis 缓存](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [迁移到认知服务 API](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk 服务 | [迁移到 Azure 应用服务的逻辑应用功能](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure 媒体服务 | [迁移到 Azure AD 身份验证](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure 备份 | [升级 Azure 备份代理](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-that-use-passive-authentication"></a>使用被动身份验证的 Web 应用程序

对于使用访问控制进行用户身份验证的 Web 应用程序，访问控制为 Web 应用程序开发人员和架构师提供以下特性和功能：

- 与 Windows Identity Foundation (WIF) 深度集成。
- 与 Google、Facebook、Yahoo、Azure Active Directory 和 ADFS 帐户，以及 Microsoft 帐户联合。
- 支持以下身份验证协议：OAuth 2.0 草案 13、WS-Trust 和 Web Services 联合身份验证（WS 联合身份验证）。
- 支持以下令牌格式：JSON Web 令牌 (JWT)、SAML 1.1、SAML 2.0 和简单 Web 令牌 (SWT)。
- WIF 中集成了主页领域发现体验，方便用户选取用于登录的帐户类型。 这种体验由 Web 应用程序托管，可完全自定义。
- 令牌转换，方便对 Web 应用程序从访问控制收到的声明进行丰富自定义，具体包括：
    - 传递标识提供者提供的声明。
    - 添加其他自定义声明。
    - 用于在特定情况下颁发声明的简单 if-then 逻辑。

遗憾的是，尚无一项服务可以等效提供所有这些功能。 应先评估所需的访问控制功能，再选择使用 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)、[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C)，还是使用其他云身份验证服务。

#### <a name="migrate-to-azure-active-directory"></a>迁移到 Azure Active Directory

可以考虑的一种途径是将应用和服务直接与 Azure AD 集成。 Azure AD 是 Microsoft 工作或学校帐户的基于云的标识提供者。 Azure AD 是 Office 365、Azure 等的标识提供者。 它提供与访问控制类似的联合身份验证功能，但并不支持所有访问控制功能。 

一个主要的例子是与社交标识提供者（如 Facebook、Google 和 Yahoo）联合。 如果用户使用这些类型的凭据进行登录，那么 Azure AD 并不适用。 

Azure AD 也未必支持与访问控制完全相同的身份验证协议。 例如，虽然访问控制和 Azure AD 都支持 OAuth，但两者的实现存在细微差异。 不同的实现要求在迁移过程中修改代码。

不过，Azure AD 确实为访问控制客户带来了多项潜在优势。 它本身支持云中托管的 Microsoft 工作或学校帐户，而访问控制客户常常使用这些帐户。 

还可通过 AD FS 将 Azure AD 租户联合到本地 Active Directory 的一个或多个实例。 这样，应用便可对基于云的用户和本地托管的用户进行身份验证。 它还支持 WS 联合身份验证协议，这样可以相对简单地使用 WIF 与 Web 应用程序进行集成。

下表对与 Web 应用程序相关的访问控制功能和 Azure AD 中提供的功能进行了比较。 

大致看来，如果只允许用户使用 Microsoft 工作或学校帐户进行登录，那么迁移到 Azure Active Directory 或许是最佳选择。

| 功能 | 访问控制支持 | Azure AD 支持 |
| ---------- | ----------- | ---------------- |
| **帐户类型** | | |
| Microsoft 工作或学校帐户 | 支持 | 支持 |
| Windows Server Active Directory 和 ADFS 帐户 |- 支持（通过与 Azure AD 租户联合） <br />- 支持（通过与 AD FS 直接联合） | 支持（只通过与 Azure AD 租户联合） | 
| 其他企业标识管理系统帐户 |- 可能支持（通过与 Azure AD 租户联合） <br />- 支持（通过直接联合） | 支持（通过与 Azure AD 租户联合） |
| 供个人使用的 Microsoft 帐户 | 支持 | 支持（只通过 Azure AD v2.0 OAuth 协议） | 
| Facebook、Google、Yahoo 帐户 | 支持 | 都不支持 |
| **协议和 SDK 兼容性** | | |
| WIF | 支持 | 支持，但说明有限 |
| WS 联合身份验证 | 支持 | 支持 |
| OAuth 2.0 | 支持草案 13 | 支持最新规范 RFC 6749 |
| WS-Trust | 支持 | 不支持 |
| **令牌格式** | | |
| JWT | 支持 beta 版本 | 支持 |
| SAML 1.1 | 支持 | 预览 |
| SAML 2.0 | 支持 | 支持 |
| SWT | 支持 | 不支持 |
| **自定义** | | |
| 可自定义的主页领域发现/帐户选取 UI | 可以合并到应用的可下载代码 | 不支持 |
| 上传自定义令牌签名证书 | 支持 | 支持 |
| 自定义令牌中的声明 |- 传递标识提供者提供的输入声明<br />- 以声明形式获取标识提供者提供的访问令牌<br />- 根据输入声明值颁发输出声明<br />- 颁发含常数值的输出声明 |- 无法传递联合标识提供者提供的声明<br />- 无法以声明形式获取标识提供者提供的访问令牌<br />- 无法根据输入声明值颁发输出声明<br />- 可以颁发含常数值的输出声明<br />- 可以根据同步到 Azure AD 的用户属性颁发输出声明 |
| **自动化** | | |
| 自动执行配置和管理任务 | 支持（通过访问控制管理服务） | 支持（通过 Microsoft Graph 和 Azure AD 图形 API） |

如果确定 Azure AD 是应用程序和服务的最佳迁移途径，应注意将应用与 Azure AD 集成的方法有两种。

若要使用 WS 联合身份验证或 WIF 来与 Azure AD 集成，建议采用[为非库应用程序配置联合单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)中所述的方法。 虽然这篇文章介绍的是如何为 Azure AD 配置基于 SAML 的单一登录，但同样也适用于配置 WS 联合身份验证。 遵循这种方法需要使用 Azure AD Premium 许可证。 这种方法具有两个优势：

- 可以完全灵活地自定义 Azure AD 令牌。 可以自定义 Azure AD 发布的声明，使其与访问控制发布的声明相匹配。 尤其是用户 ID 或名称标识符声明。 若要在更换技术后，仍继续为用户接收一致的用户标识符，需确保 Azure AD 和访问控制颁发的用户 ID 一致。
- 可以配置应用程序专属令牌签名证书，且由自己控制其生存期。

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> 这种方法需要使用 Azure AD Premium 许可证。 如果你是访问控制客户，并且需要 Premium 许可证来设置应用程序的单一登录，请与我们联系。 我们很乐意为你提供开发人员许可证。

另一种方法是遵循[此代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)，其中有关如何设置 WS 联合身份验证的说明略有不同。 此代码示例不使用 WIF，而是使用 ASP.NET 4.5 OWIN 中间件。 不过，应用注册说明也适用于使用 WIF 的应用，但不需要使用 Azure AD Premium 许可证。 

如果采用这种方法，需要了解 [Azure AD 中的签名密钥滚动更新](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 这种方法使用 Azure AD 全局签名密钥来颁发令牌。 默认情况下，WIF 不会自动刷新签名密钥。 如果 Azure AD 轮播全局签名密钥，WIF 实现需要做好接受更改的准备。

如果能够通过 OpenID Connect 或 OAuth 协议与 Azure AD 集成，则建议这样做。 [Azure AD 开发人员指南](http://aka.ms/aaddev)中收录了大量有关如何将 Azure AD 集成到 Web 应用程序的文档和指南。

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>迁移到 Azure Active Directory B2C

可以考虑的另一种途径是迁移到 Azure AD B2C。 Azure AD B2C 是类似于访问控制的云身份验证服务，可方便开发人员将身份验证和标识管理逻辑外包到云服务。 它是一项专为面向使用者的应用程序（最多有数百万名活跃用户）设计的付费服务（包含免费层和高级层）。

类似于访问控制，Azure AD B2C 最具吸引力的功能之一是，支持许多不同类型的帐户。 Azure AD B2C 允许用户使用其 Microsoft 帐户或 Facebook、Google、LinkedIn、GitHub、Yahoo 等帐户登录。 Azure AD B2C 还支持用户专门为应用程序创建的“本地帐户”或用户名和密码。 Azure AD B2C 还提供丰富扩展性，可用于自定义登录流。 

但是，Azure AD B2C 支持的身份验证协议和令牌格式范围无法满足访问控制客户的全部需求。 也不能使用 Azure AD B2C 从标识提供者、Microsoft 或其他地方获取令牌和查询附加用户信息。

下表对与 Web 应用程序相关的访问控制功能和 Azure AD B2C 中提供的功能进行了比较。 粗略来看，如果应用面向使用者或支持许多不同类型的帐户，那么正确选择可能是迁移到 Azure AD B2C。

| 功能 | 访问控制支持 | Azure AD B2C 支持 |
| ---------- | ----------- | ---------------- |
| **帐户类型** | | |
| Microsoft 工作或学校帐户 | 支持 | 支持（通过自定义策略）  |
| Windows Server Active Directory 和 ADFS 帐户 | 支持（通过使用 AD FS 进行直接联合） | 支持（通过使用自定义策略实现 SAML 联合） |
| 其他企业标识管理系统帐户 | 支持（通过使用 WS 联合身份验证进行直接联合） | 支持（通过使用自定义策略实现 SAML 联合） |
| 供个人使用的 Microsoft 帐户 | 支持 | 支持 | 
| Facebook、Google、Yahoo 帐户 | 支持 | 本身支持 Facebook 和 Google，使用自定义策略通过 OpenID Connect 联合支持 Yahoo |
| **协议和 SDK 兼容性** | | |
| Windows Identity Foundation (WIF) | 支持 | 不支持 |
| WS 联合身份验证 | 支持 | 不支持 |
| OAuth 2.0 | 支持草案 13 | 支持最新规范 RFC 6749 |
| WS-Trust | 支持 | 不支持 |
| **令牌格式** | | |
| JWT | 支持 beta 版本 | 支持 |
| SAML 1.1 | 支持 | 不支持 |
| SAML 2.0 | 支持 | 不支持 |
| SWT | 支持 | 不支持 |
| **自定义** | | |
| 可自定义的主页领域发现/帐户选取 UI | 可以合并到应用的可下载代码 | 完全可自定义 UI（通过自定义 CSS） |
| 上传自定义令牌签名证书 | 支持 | 自定义签名密钥（而不是证书），支持（通过自定义策略） |
| 自定义令牌中的声明 |- 传递标识提供者提供的输入声明<br />- 以声明形式获取标识提供者提供的访问令牌<br />- 根据输入声明值颁发输出声明<br />- 颁发含常数值的输出声明 |- 可传递标识提供者提供的声明；某些声明要求自定义政策<br />- 无法以声明形式获取标识提供者提供的访问令牌<br />- 可以通过自定义策略根据输入声明值颁发输出声明<br />- 可以通过自定义策略颁发含常数值的输出声明 |
| **自动化** | | |
| 自动执行配置和管理任务 | 支持（通过访问控制管理服务） |- 允许通过 Azure AD 图形 API 创建用户<br />- 无法以编程方式创建 B2C 租户、应用程序或策略 |

如果确定 Azure AD B2C 是应用程序和服务的最佳迁移途径，请从以下资源着手：

- [Azure AD B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="migrate-to-ping-identity-or-auth0"></a>迁移到 Ping 标识或 Auth0

在某些情况下，可能会发现如果不修改主要代码，则在 Web 应用程序中，Azure AD 和 Azure AD B2C 不足以替代访问控制。 一些常见示例可能包括：

- 通过结合利用 WIF 或 WS 联合身份验证和社交标识提供者（如 Google 或 Facebook）实现登录的 Web 应用程序。
- 通过 WS 联合身份验证协议执行与企业标识提供者的直接联合的 Web 应用程序。
- 需要将社交标识提供者（如 Google 或 Facebook）颁发的访问令牌用作访问控制颁发的令牌中的声明的 Web 应用程序。
- 具有 Azure AD 或 Azure AD B2C 无法再现的复杂令牌转换规则的 Web 应用程序。
- 具有后列特点的多租户 Web 应用程序：使用 ACS 集中管理与多种不同标识提供者的联合

在这些情况下，可能需要考虑将 Web 应用程序迁移到另一个云身份验证服务。 建议了解以下选项。 以下每个选项都提供了与访问控制类似的功能：



|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) 是一种灵活的云标识服务，该服务创建了[针对访问控制客户的高级迁移指南](https://auth0.com/acs)，并且几乎支持 ACS 所支持的所有功能。 |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping 标识](https://www.pingidentity.com)提供两种类似于 ACS 的解决方案。 PingOne 是一种云标识服务，支持多种与 ACS 相同的功能，PingFederate 是一个类似的本地标识产品，可提供更大的灵活性。 若要深入了解如何使用这些产品，请参阅 [Ping 的 ACS 停用指南](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)。  |

使用 Ping 标识和 Auth0 是为了确保所有访问控制客户都拥有适用于其应用和服务的迁移途径，从而最大限度地减少从访问控制迁移所需的工作量。

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>使用主动身份验证的 Web 服务

对于通过访问控制颁发的令牌保护的 Web 服务，访问控制提供以下特性和功能：

- 可以在访问控制命名空间中注册一个或多个服务标识。 服务标识可用于请求令牌。
- 通过使用以下类型凭据，支持 OAuth WRAP 和 OAuth 2.0 草案 13 协议请求令牌：
    - 为服务标识创建的简单密码
    - 使用对称密钥或 X509 证书的签名 SWT
    - 受信任的标识提供者（通常为 AD FS 实例）颁发的 SAML 令牌
- 支持以下令牌格式：JWT、SAML 1.1、SAML 2.0 和 SWT。
- 简单令牌转换规则。

访问控制中的服务标识通常用于实现服务器间身份验证。  

#### <a name="migrate-to-azure-active-directory"></a>迁移到 Azure Active Directory

对于这种类型的身份验证流，建议迁移到 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)。 Azure AD 是 Microsoft 工作或学校帐户的基于云的标识提供者。 Azure AD 是 Office 365、Azure 等的标识提供者。 

也可使用 Azure AD 进行服务器间身份验证（通过使用 OAuth 客户端凭据授权的 Azure AD 实现）。 下表对服务器间身份验证中的访问控制功能和 Azure AD 中提供的功能进行了比较。

| 功能 | 访问控制支持 | Azure AD 支持 |
| ---------- | ----------- | ---------------- |
| 如何注册 Web 服务 | 在访问控制管理门户中创建信赖方 | 在 Azure 门户中创建 Azure AD Web 应用程序 |
| 如何注册客户端 | 在访问控制管理门户中创建服务标识 | 在 Azure 门户中创建另一个 Azure AD Web 应用程序 |
| 使用的协议 |- OAuth WRAP 协议<br />- OAuth 2.0 草案 13 客户端凭据授予 | OAuth 2.0 客户端凭据授予 |
| 客户端身份验证方法 |- 简单密码<br />- 签名 SWT<br />- 联合标识提供者提供的 SAML 令牌 |- 简单密码<br />- 签名 JWT |
| 令牌格式 |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | 仅 JWT |
| 令牌转换 |- 添加自定义声明<br />- 简单 if-then 声明颁发逻辑 | 添加自定义声明 | 
| 自动执行配置和管理任务 | 支持（通过访问控制管理服务） | 支持（通过 Microsoft Graph 和 Azure AD 图形 API） |

若要了解如何实现服务器间方案，请参阅以下资源：

- [Azure AD 开发人员指南](https://aka.ms/aaddev)的服务间部分
- [使用简单密码客户端凭据的守护程序代码示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [使用证书客户端凭据的守护程序代码示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>迁移到 Ping 标识或 Auth0

在某些情况下，可能会发现如果不修改主要代码，在体系结构中，Azure AD 客户端凭据和 OAuth 授权实现不足以替代访问控制。 一些常见示例可能包括：

- 使用非 JWT 令牌格式的服务器间身份验证。
- 使用外部标识提供者提供的输入令牌的服务器间身份验证。
- 具有 Azure AD 无法再现的令牌转换规则的服务器间身份验证。

在这些情况下，可能要考虑将 Web 应用程序迁移到另一个云身份验证服务。 建议了解以下选项。 以下每个选项都提供了与访问控制类似的功能：

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) 是一种灵活的云标识服务，该服务创建了[针对访问控制客户的高级迁移指南](https://auth0.com/acs)，并且几乎支持 ACS 所支持的所有功能。 |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping 标识](https://www.pingidentity.com)提供两种类似于 ACS 的解决方案。 PingOne 是一种云标识服务，支持多种与 ACS 相同的功能，PingFederate 是一个类似的本地标识产品，可提供更大的灵活性。 若要深入了解如何使用这些产品，请参阅 [Ping 的 ACS 停用指南](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)。  |

使用 Ping 标识和 Auth0 是为了确保所有访问控制客户都拥有适用于其应用和服务的迁移途径，从而最大限度地减少从访问控制迁移所需的工作量。

## <a name="questions-concerns-and-feedback"></a>问题、疑虑和反馈

许多访问控制客户在阅读本文后可能仍然无法确定出合适的迁移途径。 这些客户可能会需要一些帮助或指导，以便确定出合适的计划。 若要讨论迁移方案和相关问题，请在此页上发表评论。
