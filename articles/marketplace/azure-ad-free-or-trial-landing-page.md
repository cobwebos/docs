---
title: 在商业应用商店中为免费或试用版 SaaS 服务构建登陆页面
description: 了解如何为免费或试用版 SaaS 服务构建登陆页面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: b01b482b967ba6db90aa80ba537457597fb91046
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488603"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>在商业应用商店中为免费或试用版 SaaS 服务构建登陆页面

本文将指导你完成为将在 Microsoft 商用 marketplace 上销售的免费或试用版 SaaS 应用构建登陆页的过程。

## <a name="overview"></a>概述

你可以将登陆页面视为你的软件即服务的 "会议厅" (SaaS) 产品/服务。 客户选择获取你的应用后，商业应用商店会将其定向到登陆页面，以激活并配置其订阅到 SaaS 应用程序。 创建软件即服务 (SaaS) 产品/服务时，可在合作伙伴中心中选择是否 [通过 Microsoft 进行销售](plan-saas-offer.md#listing-options)。 如果只希望在 Microsoft 商业应用商店中列出你的产品/服务，而不是通过 Microsoft 销售，则可以指定潜在客户与产品/服务交互的方式。 如果启用 " **立即获取" (免费) ** 或 " **免费试用** 列表" 选项，则必须指定一个登录页 URL，用户可以访问该 URL 来访问免费订阅或试用版。

登陆页面的目的只是接收用户，以便他们可以激活免费试用版或免费订阅。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph，你将为用户启用单一登录 (SSO) ，并获取有关用户的重要详细信息，你可以使用该用户激活免费试用版或免费订阅，包括其名称、电子邮件地址和组织。

由于激活订阅所需的信息由 Azure AD 和 Microsoft Graph 提供限制，因此，不需要请求要求超过基本许可的信息。 如果需要用户详细信息需要额外的应用程序许可，应在订阅激活完成后请求此信息。 这将为用户启用顺畅订阅激活并降低放弃的风险。

登陆页通常包含以下信息和列表选项：

- 显示免费试用或免费订阅的名称和详细信息。 例如，指定试用的使用限制或持续时间。
- 显示用户的帐户详细信息，包括名字和姓氏、组织和电子邮件。
- 提示用户确认或替换不同的帐户详细信息。
- 激活后，指导用户执行后续步骤。 例如，收到欢迎电子邮件、管理订阅、获取支持或阅读文档。

本文的以下部分将指导你完成构建登陆页面的过程：

1. 为登陆页面[创建 Azure AD 应用注册](#create-an-azure-ad-app-registration)。
2. [使用代码示例作为](#use-a-code-sample-as-a-starting-point) 你的应用程序的起点。
3. 从在登录后接收的[ID 令牌中编码的声明中读取信息](#read-information-from-claims-encoded-in-the-id-token)，该令牌是通过请求发送的 Azure AD。
4. 根据需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)收集其他信息。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

商业应用商店与 Azure AD 完全集成。 用户以 Azure AD 帐户的身份进行身份验证， [或 Microsoft 帐户 (MSA) ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)进行身份验证。 通过仅限列表的产品/服务获取免费或免费试用版订阅后，该用户将从商业市场转到登陆页 URL，以激活和管理其订阅到 SaaS 应用程序。 你必须让用户登录到你的应用程序，Azure AD SSO。  (产品/服务的 [技术配置](plan-saas-offer.md#technical-information) 页中指定了登陆页 URL。

使用该标识的第一步是确保登录页注册为 Azure AD 应用程序。 通过注册应用程序，可以使用 Azure AD 对用户进行身份验证并请求访问用户资源。 它可以被视为应用程序的定义，该定义允许服务根据应用的设置了解如何向应用程序颁发令牌。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

若要开始操作，请按照 [注册新应用程序](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的说明进行操作。 若要让其他公司的用户访问该应用，你必须选择任何 **组织目录中的帐户 (任何 Azure AD 目录， () 例如， ** 当系统询问可以使用该应用程序的情况下，你可以使用 Skype 或 Xbox) 等任意目录。

如果要查询 Microsoft Graph API，请 [将新应用程序配置为访问 Web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)。 当你为此应用程序选择 API 权限时， **用户** 的默认值就足以收集有关用户的基本信息，使载入过程平滑和自动执行。 不要请求标记为 **需要管理员同意**的任何 API 权限，因为这会阻止所有非管理员用户访问登录页。

如果你确实需要提升的权限作为加入或预配过程的一部分，请考虑使用 Azure AD 的 [增量许可](https://aka.ms/incremental-consent) 功能，以便从 marketplace 发送的所有用户都能够在最初与登陆页面交互。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用代码示例作为起点

Microsoft 提供了多个示例应用，可实现一个启用了 Azure AD 登录的简单网站。 在 Azure AD 中注册应用程序后，" **快速入门** " 边栏选项卡将提供 (图 1) 的常见应用程序类型和开发堆栈的列表。 选择与你的环境匹配的项，并按照下载和设置的说明进行操作。

***图1： Azure 门户中的快速启动边栏选项卡***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="阐释了 Azure 门户中的 快速入门 边栏选项卡。":::

下载代码并设置开发环境后，在应用程序中更改配置设置，以反映你在前面的过程中记录的应用程序 ID、租户 ID 和客户端机密。 请注意，具体步骤会有所不同，具体取决于所使用的示例。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>从 ID 令牌中编码的声明读取信息

在 [OpenID connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 流中，当用户发送到登陆页面时，Azure AD 会将 [ID 令牌](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 添加到请求。 此标记包含多个基本信息，它们在激活过程中可能有用，包括此表中所述的信息。

| 值 | 说明 |
| ------------ | ------------- |
| aud | 此令牌的目标受众。 在这种情况下，它应匹配你的应用程序 ID 并进行验证。 |
| preferred_username | 访问用户的主用户名。 这可能是电子邮件地址、电话号码或其他标识符。 |
| 电子邮件 | 用户的电子邮件地址。 请注意，此字段可能为空。 |
| name | 标识令牌使用者的可读值。 在这种情况下，它将是用户的名称。 |
| oid | Microsoft 标识系统中的标识符，用于跨应用程序唯一标识用户。 Microsoft Graph 会将此值作为给定用户帐户的 ID 属性返回。 |
| tid | 表示用户所在的 Azure AD 租户的标识符。 对于 MSA 标识，此项将始终为 `9188040d-6c67-4c5b-b112-36a304b66dad` 。 有关详细信息，请参阅下一部分中的说明：使用 Microsoft Graph API。 |
| sub | 唯一标识此特定应用程序中的用户的标识符。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

ID 令牌包含标识用户的基本信息，但您的激活过程可能需要更多详细信息（如用户的公司）才能完成载入过程。 使用 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) 请求此信息，以避免强制用户再次输入这些详细信息。 标准 **用户。读取** 权限包括以下信息，默认情况下为：

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

可以选择其他属性（例如用户公司的名称或用户在国家)  (的位置），以便在请求中包括。 有关更多详细信息，请参阅 [用户资源类型的属性](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties)。

注册到 Azure AD 的大多数应用都授予委派的权限，以便从其公司 Azure AD 租户读取用户的信息。 对该信息 Microsoft Graph 的任何请求都必须附带一个访问令牌作为身份验证。 生成访问令牌的具体步骤将取决于所使用的技术堆栈，但示例代码将包含示例。 有关详细信息，请参阅 [代表用户获取访问权限](https://docs.microsoft.com/graph/auth-v2-user)。

> [!NOTE]
> 具有租户 ID) 的 MSA 租户 (中的帐户 `9188040d-6c67-4c5b-b112-36a304b66dad` 将不会返回超过已用 ID 令牌收集的信息。 因此，您可以跳过此对这些帐户的图形 API 调用。

## <a name="next-steps"></a>后续步骤
- [如何在商业应用商店中创建 SaaS 产品/服务](create-new-saas-offer.md)
