---
title: 应用程序的最终用户体验-Azure Active Directory
description: Azure Active Directory (Azure AD) 提供多种可自定义的方式来向组织中的最终用户部署应用程序。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 8968fd54968f3115641d2315a534ba61a247a06d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90605118"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中应用程序的最终用户体验

Azure Active Directory (Azure AD) 提供多种可自定义的方式来向组织中的最终用户部署应用程序：

* Azure AD 我的应用
* Microsoft 365 应用程序启动器
* 直接登录联合应用
* 联合、基于密码或现有应用的深层链接

请自行决定要选择哪种方法在组织中进行部署。

## <a name="azure-ad-my-apps"></a>Azure AD 我的应用

我的应用程序 https://myapps.microsoft.com 是一个基于 web 的门户，它允许在 Azure Active Directory 中具有组织帐户的最终用户查看和启动 Azure AD 管理员向他们授予了访问权限的应用程序。 如果你是具有 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)的最终用户，则还可以通过 "我的应用" 使用自助服务组管理功能。

默认情况下，所有应用程序都在一个页面上一起列出。 但你可以使用集合将相关应用程序组合在一起，并将它们显示在一个单独的选项卡上，使其更易于查找。 例如，你可以使用集合为特定作业角色、任务、项目等创建应用程序的逻辑分组。 有关信息，请参阅 " [我的应用" 门户上的创建集合](access-panel-collections.md)。 

"我的应用" 与 Azure 门户分离，无需用户拥有 Azure 订阅或 Microsoft 365 订阅。

有关 Azure AD 应用的详细信息，请参阅 [我的应用简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 应用程序启动器

对于已部署 Microsoft 365 的组织，通过 Azure AD 分配给用户的应用程序也会出现在 Office 365 门户中的 [https://portal.office.com/myapps](https://portal.office.com/myapps) 。 这样，组织中的用户便可以轻松、方便地启动其应用程序，而无需使用第二个门户，而是建议的应用程序为使用 Microsoft 365 的组织启动解决方案。

有关 Office 365 应用程序启动器的详细信息，请参阅[让应用出现在 Office 365 应用启动器中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登录联合应用

大多数支持 SAML 2.0、WS 联合身份验证或 OpenID Connect 的联合应用程序也支持用户在应用程序启动，再通过 Azure AD 的自动重定向或单击链接进行登录。 这称为服务提供者发起的登录，Azure AD 应用程序库中的大多数联合应用程序都支持此 (请参阅 Azure 门户中应用的单一登录配置向导中链接的文档以获取详细信息) 。

## <a name="direct-sign-on-links"></a>直接登录链接

Azure AD 还为支持基于密码单一登录、链接单一登录以及任何形式的联合单一登录的各个应用程序提供直接单一登录链接。

这些链接是专门编写的 Url，这些 Url 通过特定应用程序的 Azure AD 登录过程发送用户，而无需用户从 Azure AD 我的应用或 Microsoft 365 启动它们。 这些 **用户访问 url** 可在可用企业应用程序的属性下找到。 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。 选择应用程序，然后选择 " **属性**"。

![Twitter 属性中的用户访问 URL 示例](media/end-user-experiences/direct-sign-on-link.png)

可以复制这些链接，并粘贴到任何想要提供选定应用程序登录链接的位置。 该位置可以是在电子邮件，或者任何已设置用户应用程序访问权限的自定义 Web 门户。 以下是 Twitter 的 Azure AD 直接单一登录 URL 示例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

类似于应用程序的组织特定 Url，你可以在 *myapps.microsoft.com* 域之后为你的目录添加一个活动的或经过验证的域，从而进一步自定义此 URL。 这样可以确保用户无需事先输入其用户 ID，登录页就可以立即加载任何组织品牌：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

当已授权的用户单击其中一个应用程序特定的链接时，他们会先看到其组织登录页， (假设他们尚未登录) ，登录后会重定向到应用程序，而无需先停止应用程序。 如果用户未安装访问应用程序所需的必备组件（例如基于密码的单一登录浏览器扩展），链接会提示用户安装缺少的扩展。 如果应用程序的单一登录配置发生更改，链接 URL 也会保持不变。

这些链接使用与我的应用和 Microsoft 365 相同的访问控制机制，并且只有已分配到 Azure 门户中的应用程序的用户或组才能成功进行身份验证。 不过，任何未经授权的用户都会看到一条消息，说明他们未获得访问权限，并为其提供加载应用程序的链接，以查看他们有权访问的可用应用程序。

## <a name="next-steps"></a>后续步骤

* [应用程序管理的快速入门系列](view-applications-portal.md)
* [什么是单一登录？](what-is-single-sign-on.md)
* [将 Azure Active Directory 与应用程序集成入门指南](plan-an-application-integration.md)
