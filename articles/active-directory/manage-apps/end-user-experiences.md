---
title: 应用程序的最终用户体验-Azure Active Directory
description: Azure Active Directory （Azure AD）提供了多种可自定义的方法，可用于将应用程序部署到组织中的最终用户。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9ff446bae0a010b12e84172d5af60c6ca81c0e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896896"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中的应用程序的最终用户体验

Azure Active Directory （Azure AD）提供了多种可自定义的方法，可用于将应用程序部署到组织中的最终用户：

* Azure AD 访问面板
* Office 365 应用程序启动器
* 直接登录联合应用
* 联合、基于密码或现有应用的深层链接

选择要在组织中部署的方法是你的决定。

## <a name="azure-ad-access-panel"></a>Azure AD 访问面板

https://myapps.microsoft.com 的访问面板是一个基于 web 的门户，它允许在 Azure Active Directory 中具有组织帐户的最终用户查看和启动 Azure AD 管理员向他们授予了访问权限的基于云的应用程序。 如果你是具有[Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)的最终用户，则还可以通过访问面板利用自助服务组管理功能。

![屏幕截图显示 Azure AD 访问面板门户](media/what-is-single-sign-on/azure-ad-access-panel.png)

默认情况下，所有应用程序都在一个页面上一起列出。 但你可以使用集合将相关应用程序组合在一起，并将它们显示在一个单独的选项卡上，使其更易于查找。 例如，你可以使用集合为特定作业角色、任务、项目等创建应用程序的逻辑分组。 有关信息，请参阅[如何使用我的应用集合自定义用户访问面板](access-panel-collections.md)。 

访问面板与 Azure 门户分离，无需用户拥有 Azure 订阅或 Office 365 订阅。

有关 Azure AD 访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="office-365-application-launcher"></a>Office 365 应用程序启动器

对于已部署 Office 365 的组织，通过 Azure AD 分配给用户的应用程序也会出现在 Office 365 门户的[https://portal.office.com/myapps](https://portal.office.com/myapps)中。 这样，组织中的用户便可以轻松、方便地启动其应用程序，而无需使用第二个门户，而是建议的应用程序为使用 Office 365 的组织启动解决方案。

![屏幕截图显示 Office 365 门户](./media/end-user-experiences/microsoft-365-portal-office-com.png)

有关 Office 365 应用程序启动器的详细信息，请参阅[让应用出现在 Office 365 应用启动器中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登录联合应用

大多数支持 SAML 2.0、WS 联合身份验证或 OpenID connect 的联合应用程序也支持用户在应用程序上启动，然后通过自动重定向或单击链接进行登录，Azure AD。 这称为服务提供者发起的登录，Azure AD 应用程序库中的大多数联合应用程序都支持这种方式（有关详细信息，请参阅 Azure 门户中的应用的单一登录配置向导中链接的文档）。

![移动应用登录页的示例](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>直接登录链接

Azure AD 还支持指向单个应用程序的直接单一登录链接，这些应用程序支持基于密码的单一登录、链接单一登录以及任何形式的联合单一登录。

这些链接是专门编写的 Url，这些 Url 通过特定应用程序的 Azure AD 登录过程发送用户，而无需用户从 Azure AD 访问面板或 Office 365 启动它们。 这些**用户访问 url**可在可用企业应用程序的属性下找到。 在 Azure 门户中，选择 " **Azure Active Directory** " > "**企业应用程序**"。 选择应用程序，然后选择 "**属性**"。

![Twitter 属性中的用户访问 URL 示例](media/end-user-experiences/direct-sign-on-link.png)

可以复制这些链接，并粘贴到任何想要提供选定应用程序登录链接的位置。 这可能是电子邮件，也可能是你为用户应用程序访问设置的任何基于 web 的自定义门户。 下面是适用于 Twitter 的 Azure AD direct 单一登录 URL 的示例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

类似于访问面板的组织特定 Url，可以在 myapps.microsoft.com 域之后为目录添加一个活动的或经过验证的域，从而进一步自定义此 URL。 这可确保在登录页面上立即加载任何组织品牌，用户无需首先输入其用户 ID：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

当已授权的用户单击其中一个应用程序特定的链接时，他们会先看到其组织登录页（假设他们尚未登录），登录之后将重定向到其应用程序，而无需先停止访问面板。 如果用户在访问应用程序时缺少先决条件（例如基于密码的单一登录浏览器扩展），链接会提示用户安装缺少的扩展。 如果应用程序的单一登录配置发生更改，链接 URL 也会保持不变。

这些链接使用与访问面板和 Office 365 相同的访问控制机制，并且只有已分配到 Azure 门户中的应用程序的用户或组才能成功进行身份验证。 不过，任何未经授权的用户都会看到一条消息，说明他们未获得访问权限，并为其提供加载访问面板的链接，以查看他们有权访问的可用应用程序。

## <a name="next-steps"></a>后续步骤

有关部署计划，请参阅[Azure Active Directory 部署计划](../fundamentals/active-directory-deployment-plans.md)
