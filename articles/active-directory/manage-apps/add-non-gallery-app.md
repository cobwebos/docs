---
title: 添加非库应用程序-Microsoft 标识平台 |Microsoft Docs
description: 将非库应用程序添加到 Azure AD 租户。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477263"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>向 Azure AD 组织添加未列出的 (非库) 应用程序

除了[Azure AD 应用程序库](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中的选项外, 还可以选择添加**非库应用程序**。 可以添加组织中已存在的任何应用程序, 也可以添加不是 Azure AD 库的一部分的供应商的任何第三方应用程序。 根据您的[许可协议](https://azure.microsoft.com/pricing/details/active-directory/), 可以使用以下功能:

- 自助集成支持[安全断言标记语言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0)标识提供程序的任何应用程序 (SP 启动或 IdP 启动)
- 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](what-is-single-sign-on.md#password-based-sso) 的任何 Web 应用程序
- 使用[系统实现跨域标识管理 (SCIM) 协议进行用户预配](use-scim-to-provision-users-and-groups.md)的应用程序的自助服务连接
- 可在 [Office 365 应用启动器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 访问面板](what-is-single-sign-on.md#linked-sign-on)中添加任何应用程序的链接

本文介绍如何在不编写代码的情况下, 将非库应用程序添加到 Azure 门户中的**企业应用程序**。 如果你要查找有关如何将自定义应用与 Azure AD 集成的开发人员指南, 请参阅[Azure AD 的身份验证方案](../develop/authentication-scenarios.md)。 当你开发使用一种新式协议 (如[OpenId connect/OAuth](../develop/active-directory-v2-protocols.md) ) 对用户进行身份验证的应用时, 你可以使用 Azure 门户中的[应用注册](../develop/quickstart-register-app.md)体验将其注册到 Microsoft 标识平台。

## <a name="add-a-non-gallery-application"></a>添加非库应用程序

1. 使用你的 Microsoft 标识平台管理员帐户登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。
1. 选择 "**企业应用程序** > " "**新建应用程序**"。
2. (可选, 但建议使用)在 "**从库中添加"** 搜索框中, 输入应用程序的显示名称。 如果应用程序出现在搜索结果中, 请选择它, 并跳过此过程的其余部分。
3. 选择 "**非库应用程序**"。 此时将显示 "**添加自己的应用程序**" 页。

   ![添加应用程序](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. 输入新应用程序的显示名称。
6. 选择 **添加** 。 此时将打开 "应用程序**概述**" 页。

## <a name="configure-user-sign-in-properties"></a>配置用户登录属性

1. 选择“属性”，以打开属性页进行编辑。

    ![“编辑属性”窗格](media/add-application-portal/edit-properties.png)

1. 设置以下选项, 以确定分配给或未分配到应用程序的用户如何登录到应用程序, 以及用户是否可以在访问面板中看到该应用程序。

    - “启用以供用户登录”决定了分配给应用程序的用户能否登录。
    - “需要进行用户分配”决定了未分配给应用程序的用户能否登录。
    - “对用户可见”决定了分配给应用的用户能否在访问面板和 O365 启动器中看到它。

      **已分配**用户的行为：

       | 应用程序属性设置 | | | 已分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 已分配用户能否登录? | 已分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

      **未分配**用户的行为：

       | 应用程序属性设置 | | | 未分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要使用用户分配? | 对用户可见? | 未分配用户能否登录? | 未分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *用户能否在访问面板和 Office 365 应用启动器中看到应用程序?

1. 若要使用自定义徽标, 请创建 215 x 215 像素的徽标, 并将其保存为 PNG 格式。 然后浏览到你的徽标并上传。

    ![更改徽标](media/add-application-portal/change-logo.png)

1. 完成后，选择“保存”。

## <a name="next-steps"></a>后续步骤

现在, 你已将应用程序添加到 Azure AD 组织, 请选择想要使用的[单一登录方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method), 并参阅以下文章:

- [配置基于 SAML 的单一登录](configure-single-sign-on-non-gallery-applications.md)
- [配置密码单一登录](configure-password-single-sign-on-non-gallery-applications.md)
- [配置链接登录](configure-linked-sign-on.md)
