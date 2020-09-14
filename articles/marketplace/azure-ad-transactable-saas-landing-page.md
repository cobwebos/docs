---
title: 在商业应用商店中构建事务 SaaS 产品/服务的登陆页面
description: 了解如何为事务 SaaS 产品/服务构建登陆页面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 9db013d13098fc6aa4552459a2189e0ad8fc3ea6
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378791"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>在商业应用商店中构建事务 SaaS 产品/服务的登陆页面

本文将指导你完成构建事务 SaaS 应用的登陆页面的过程，该应用将在 Microsoft 商业市场上出售。

## <a name="overview"></a>概述

你可以将登陆页面视为你的软件即服务的 "会议厅" (SaaS) 产品/服务。 买家订阅产品/服务后，商业应用商店会将其定向到登陆页面，以激活并配置其对 SaaS 应用程序的订阅。 将此视为订单确认步骤，该步骤允许买方查看他们购买的内容并确认其帐户详细信息。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph，你将为买家启用单一登录 (SSO) ，并获取有关可用于确认和激活其订阅的买方的重要详细信息，包括其名称、电子邮件地址和组织。

由于激活订阅所需的信息由 Azure AD 和 Microsoft Graph 提供限制，因此，不需要请求要求超过基本许可的信息。 如果需要用户详细信息需要额外的应用程序许可，应在订阅激活完成后请求此信息。 这可为买方启用顺畅订阅激活，并降低放弃的风险。

登陆页面通常包括以下内容：

- 提供购买的产品/服务的名称，以及计费条款。
- 提供买方帐户的详细信息，包括名字和姓氏、组织和电子邮件。
- 提示买方确认或替换不同的帐户详细信息。
- 激活后，指导买方执行后续步骤。 例如，收到欢迎电子邮件、管理订阅、获取支持或阅读文档。

> [!NOTE]
> 激活后，买家还会定向到登录页。 激活买家订阅后，必须使用 SSO 使用户能够登录。 建议将用户定向到帐户配置文件或配置页面。

以下部分将指导你完成构建登陆页面的过程：

1. 为登陆页面[创建 Azure AD 应用注册](#create-an-azure-ad-app-registration)。
1. [使用代码示例作为](#use-a-code-sample-as-a-starting-point) 你的应用程序的起点。
1. [使用两个 Azure AD 应用提高生产中的安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。
1. 解决商业应用商店添加到 URL[的 marketplace 购买标识令牌](#resolve-the-marketplace-purchase-identification-token)。
1. [从 ID 令牌中编码的声明](#read-information-from-claims-encoded-in-the-id-token)（在登录后从 Azure AD 收到）中读取信息，该令牌与请求一起发送。
1. 根据需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)收集其他信息。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

商业应用商店与 Azure AD 完全集成。 买家会接收到使用 Azure AD 帐户进行身份验证的 marketplace， [或 Microsoft 帐户 (MSA) ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)。 购买后，买家从商业应用商店进入登陆页面 URL，以激活和管理其 SaaS 应用程序的订阅。 必须让买家通过 Azure AD SSO 登录到你的应用程序。  (产品/服务的 [技术配置](plan-saas-offer.md#technical-information) 页中指定了登陆页 URL。

使用该标识的第一步是确保登录页注册为 Azure AD 应用程序。 通过注册应用程序，可以使用 Azure AD 对用户进行身份验证并请求访问用户资源。 它可以被视为应用程序的定义，该定义允许服务根据应用的设置了解如何向应用程序颁发令牌。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

若要开始操作，请按照 [注册新应用程序](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的说明进行操作。 若要让其他公司的用户访问该应用，必须在系统询问谁可以使用该应用程序时，选择一个多租户选项。

如果要查询 Microsoft Graph API，请 [将新应用程序配置为访问 Web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)。 当你为此应用程序选择 API 权限时， **用户** 的默认值就足以收集有关买家的基本信息，使载入过程平滑和自动执行。 不要请求标记为 **需要管理员同意**的任何 API 权限，因为这会阻止所有非管理员用户访问登录页。

如果你需要将提升的权限作为载入或预配过程的一部分，请考虑使用 Azure AD 的 [增量许可](https://aka.ms/incremental-consent) 功能，以便从 marketplace 发送的所有买家最初都能够与登陆页面交互。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用代码示例作为起点

我们提供了几个示例应用，用于实现一个启用了 Azure AD 登录的简单网站。 在 Azure AD 中注册应用程序后，" **快速入门** " 边栏选项卡会提供常见应用程序类型和开发堆栈的列表，如图1所示。 选择与你的环境匹配的项，并按照下载和设置的说明进行操作。

***图1： Azure 门户中的快速启动边栏选项卡***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="阐释了 Azure 门户中的 快速入门 边栏选项卡。":::

下载代码并设置开发环境后，在应用程序中更改配置设置，以反映你在前面的过程中记录的应用程序 ID、租户 ID 和客户端机密。 请注意，具体步骤会有所不同，具体取决于所使用的示例。

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>使用两个 Azure AD 应用提高生产中的安全性

本文介绍体系结构的简化版本，用于实现商业 marketplace SaaS 产品的登陆页面。 在生产环境中运行该页面时，我们建议你仅通过其他受保护的应用程序与 SaaS 履单 Api 通信，提高安全性。 这需要创建两个新的应用程序：

- 首先，在此时间点介绍了多租户登录页应用程序，但没有联系 SaaS 履单 Api 的功能除外。 此功能将被卸载到另一应用程序，如下所述。
- 其次，是一种应用程序，负责与 SaaS 履单 Api 进行通信。 此应用程序应为单租户，且仅供你的组织使用，可以建立访问控制列表，以仅限从此应用对 Api 的访问权限。

这使解决方案能够在观察 [问题原则分离](https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) 的情况下工作。 例如，登陆页面使用第一个注册 Azure AD 应用来登录用户。 用户登录后，登陆页面将使用第二个 Azure AD 来请求访问令牌，以调用 SaaS 履单 API 并调用解析操作。

## <a name="resolve-the-marketplace-purchase-identification-token"></a>解析 marketplace 购买标识令牌

将买家发送到登陆页面后，会向 URL 参数添加一个令牌。 此令牌不同于 Azure AD 颁发的令牌和用于服务到服务身份验证的访问令牌，它用作 [SaaS 履单 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 解析调用的输入，以获取订阅的详细信息。 与对 SaaS 履单执行 Api 的所有调用一样，服务到服务请求将使用基于应用的 Azure AD 应用程序 ID 用户进行服务到服务身份验证的访问令牌进行身份验证。

> [!NOTE]
> 在大多数情况下，最好从第二个单租户应用程序进行此调用。 请参阅本文前面的 [使用两个 Azure AD 应用以提高生产中的安全性](#use-two-azure-ad-apps-to-improve-security-in-production) 。

### <a name="request-an-access-token"></a>请求访问令牌

若要使用 SaaS 履单 Api 对应用程序进行身份验证，需要一个访问令牌，该令牌可以通过调用 Azure AD OAuth 终结点生成。 请参阅 [如何获取发布者的授权令牌](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

### <a name="call-the-resolve-endpoint"></a>调用解析终结点

SaaS 履单 Api 实现了 [解析](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 终结点，可以调用该终结点来确认 marketplace 令牌的有效性并返回有关订阅的信息。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>从 ID 令牌中编码的声明读取信息

作为 [OpenID connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 流程的一部分，在将买家发送到登陆页面时，Azure AD 会向请求添加一个 [ID 令牌](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 。 此标记包含多个基本信息，它们在激活过程中可能有用，包括此表中所述的信息。

| 值 | 说明 |
| ------------ | ------------- |
| aud | 此令牌的目标受众。 在这种情况下，它应匹配你的应用程序 ID 并进行验证。 |
| preferred_username | 访问用户的主用户名。 这可能是电子邮件地址、电话号码或其他标识符。 |
| 电子邮件 | 用户的电子邮件地址。 请注意，此字段可能为空。 |
| name | 标识令牌使用者的可读值。 在这种情况下，它将是买家的名称。 |
| oid | Microsoft 标识系统中的标识符，用于跨应用程序唯一标识用户。 Microsoft Graph 会将此值作为给定用户帐户的 ID 属性返回。 |
| tid | 表示买方所在 Azure AD 租户的标识符。 对于 MSA 标识，此项将始终为 ``9188040d-6c67-4c5b-b112-36a304b66dad`` 。 有关详细信息，请参阅下一部分中的说明：使用 Microsoft Graph API。 |
| sub | 唯一标识此特定应用程序中的用户的标识符。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

ID 令牌包含用于识别买方的基本信息，但您的激活过程可能需要更多详细信息（例如买方公司）来完成载入过程。 使用 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) 请求此信息，以避免强制用户再次输入这些详细信息。 标准 **用户。读取** 权限在默认情况下包括以下信息。

| 值 | 说明 |
| ------------ | ------------- |
| displayName | 在用户的通讯簿中显示的名称。 |
| givenName | 用户的名字。 |
| jobTitle | 用户的职务。 |
| mail | 用户的 SMTP 地址。 |
| mobilePhone | 用户的主移动电话号码。 |
| preferredLanguage | 用户首选语言的 ISO 639-1 代码。 |
| surname | 用户的姓氏。 |
|||

可以选择其他属性（例如用户公司的名称或用户在国家)  (的位置），以便在请求中包括。 有关更多详细信息，请参阅 [用户资源类型的属性](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) 。

注册到 Azure AD 的大多数应用都授予委派的权限，以便从其公司 Azure AD 租户读取用户的信息。 对该信息 Microsoft Graph 的任何请求都必须附带用于身份验证的访问令牌。 生成访问令牌的具体步骤将取决于所使用的技术堆栈，但示例代码将包含示例。 有关详细信息，请参阅 [代表用户获取访问权限](https://docs.microsoft.com/graph/auth-v2-user)。

> [!NOTE]
> 具有租户 ID) 的 MSA 租户 (中的帐户 ``9188040d-6c67-4c5b-b112-36a304b66dad`` 将不会返回超过已用 ID 令牌收集的信息。 因此，您可以跳过此对这些帐户的图形 API 调用。

## <a name="next-steps"></a>后续步骤

- [如何在商业应用商店中创建 SaaS 产品/服务](create-new-saas-offer.md)
