---
title: 添加库应用 - Azure Active Directory | Microsoft Docs
description: 了解如何将 Azure AD 库中的应用添加到 Azure 企业应用程序。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477285"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>将库应用添加到 Azure AD 组织

Azure Active Directory (Azure AD) 有一个库，其中包含数千个已启用企业单一登录的预集成应用程序。 本文介绍从库将应用添加到 Azure AD 组织的常规步骤。

> [!IMPORTANT]
> 首先，在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中查看你的应用。 你可能会找到添加和配置要添加的库应用的分步指南。

## <a name="add-a-gallery-application"></a>添加库应用程序

1. 以 Azure AD 租户全局管理员、云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”  。

1. 在“Azure Active Directory”窗格中，选择“企业应用程序”。  

    ![打开企业应用程序](media/add-application-portal/open-enterprise-apps.png)

1. 选择“新建应用程序”  。

    ![新建应用程序](media/add-application-portal/new-application.png)

1. 在“从库中添加”  下的搜索框中，输入要添加的应用程序的名称。 

    ![按名称或类别搜索](media/add-application-portal/categories.png)

1. 从结果中选择应用程序。

1. （可选）在特定于应用程序的表单中，可以根据组织需要编辑应用程序的名称。

1. 选择 **添加** 。 此时会打开应用程序的“概述”  页。

## <a name="configure-user-sign-in-properties"></a>配置用户登录属性

1. 选择“属性”，以打开属性页进行编辑。 

    ![“编辑属性”窗格](media/add-application-portal/edit-properties.png)

1. 设置以下选项，以确定已分配或未分配给应用程序的用户如何登录应用程序，以及用户是否可以在访问面板中看到该应用程序。

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
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 未分配用户能否登录? | 未分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *用户能否在访问面板和 Office 365 应用启动器中看到应用程序?

1. 若要使用自定义徽标，请创建 215 x 215 像素的徽标，并将其保存为 PNG 格式。 然后浏览到你的徽标并将其上传。

    ![更改徽标](media/add-application-portal/change-logo.png)

1. 完成后，选择“保存”  。

## <a name="next-steps"></a>后续步骤

现在你已将应用程序添加到 Azure AD 组织，请[选择要使用的单一登录方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)，并参考下面的相应文章：

- [配置基于 SAML 的单一登录](configure-single-sign-on-non-gallery-applications.md)
- [配置密码单一登录](configure-password-single-sign-on-non-gallery-applications.md)
- [配置链接登录](configure-linked-sign-on.md)

