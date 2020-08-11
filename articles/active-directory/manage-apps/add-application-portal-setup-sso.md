---
title: 快速入门：在 Azure Active Directory (Azure AD) 租户中为应用程序设置单一登录 (SSO)
description: 本快速入门逐步介绍在 Azure Active Directory (Azure AD) 租户中为应用程序设置单一登录 (SSO) 的过程。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 461a424f23161e1567c6b32a38db0225efc56b1e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808382"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入门：在 Azure Active Directory (Azure AD) 租户中为应用程序设置单一登录 (SSO)

通过为添加到 Azure Active Directory (Azure AD) 租户的应用程序设置单一登录 (SSO)，开始简化用户登录。 设置 SSO 后，用户可以使用其 Azure AD 凭据登录到应用程序。 SSO 在 Azure AD 免费版中提供。

## <a name="prerequisites"></a>先决条件

若要为添加到 Azure AD 租户的应用程序设置 SSO，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 支持 SSO 且已预配置并添加到 Azure AD 库的应用程序。 大多数应用都可以使用 Azure AD 进行 SSO。 Azure AD 库中的应用是预配置的。 如果应用未列出或是自定义开发的应用，则仍可以将其与 Azure AD 一起使用。 查看目录中的教程和其他文档。 本快速入门重点介绍已针对 SSO 进行预配置并已由应用开发人员添加到 Azure AD 库的应用。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。
- 可选：完成[配置应用](add-application-portal-configure.md)。


>[!IMPORTANT]
>使用非生产性环境测试本快速入门中的步骤。


## <a name="enable-single-sign-on-for-an-app"></a>为应用启用单一登录

将应用程序添加到 Azure AD 租户后，将显示“概述”页。 如果要配置已添加的应用程序，请查看第一个快速入门。 它将引导你查看已添加到租户的应用程序。 

为应用程序配设置单一登录：

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要设置单一登录的应用程序。
1. 在“管理”部分中，选择“单一登录”，打开“单一登录”窗格进行编辑  。

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="屏幕截图显示 Azure AD 门户中的“单一登录配置”页。":::

1. 选择“SAML”以打开“SSO 配置”页。 在此示例中，我们配置进行 SSO 的应用程序是 GitHub。 设置 GitHub 后，用户可以使用 Azure AD 租户中自己的凭据登录 GitHub。

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="屏幕截图显示 GitHub 上的“单一登录配置”页。":::

1. 将应用程序配置为使用 Azure AD 进行基于 SAML 的 SSO 的过程因应用程序而异。 本文提供指向 GitHub 指南的链接。 若要查找其他应用的指南，请参阅[有关将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/)。
1. 请按照指南为应用程序设置 SSO。 许多应用程序对 SSO 功能都有特定的订阅要求。 例如，GitHub 需要企业订阅。
    > [!TIP]
    > 若要详细了解 SAML 配置选项，请参阅[配置基于 SAML 的单一登录](configure-saml-single-sign-on.md)。

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="屏幕截图显示 GitHub 定价页面的企业订阅中的“单一登录”选项。":::


> [!TIP]
> 可使用 Graph API 自动管理应用，具体请参阅[使用 Microsoft Graph API 自动管理应用](https://docs.microsoft.com/graph/application-saml-sso-configure-api)。


## <a name="clean-up-resources"></a>清理资源

完成本快速入门系列后，请考虑删除应用以清理测试租户。 本系列中的最后一个快速入门介绍如何删除应用，请参阅[删除应用](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章了解如何删除应用。
> [!div class="nextstepaction"]
> [删除应用](delete-application-portal.md)
