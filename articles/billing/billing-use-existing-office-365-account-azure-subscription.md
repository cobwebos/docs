---
title: "使用 Office 365 帐户注册 Azure | Microsoft 文档"
description: "了解如何使用 Office 365 帐户创建 Azure 订阅"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.contentlocale: zh-cn
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>使用 Office 365 帐户注册 Azure 订阅
如果具有 Office 365 订阅，使用 Office 365 帐户创建 Azure 订阅。 可以在没有 Azure 订阅的情况下使用 Office 365 用户名和密码登录 [Azure 门户](https://portal.azure.com/)。 但如果想要设置虚拟机或使用其他 Azure 服务，则必须为帐户创建 Azure 订阅。 如果是想使用 Azure 服务的组织的成员，在创建订阅后将组织中的其他人员添加到订阅中。  

如果已具有 Office 365 帐户和 Azure 订阅，请参阅[将 Office 365 租户与 Azure 订阅关联](billing-add-office-365-tenant-to-azure-subscription.md)。

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>通过 Office 365 帐户获取 Azure 订阅

若要注册 Azure，请使用 Azure 免费链接，并使用 Office 365 用户名和密码注册 Azure。 

1. 转到 [Azure.com](https://azure.microsoft.com/)。 
2. 单击“免费开始”。
3. 使用 Office 365 用户名和密码登录。 所使用的帐户无需具有管理员权限。 如果具有多个 Office 365 帐户，请确保使用想要与 Azure 订阅关联的 Office 365 帐户的凭据。 

   ![显示登录页的屏幕截图。](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. 填写所需信息并完成注册过程。

    ![显示注册表单的屏幕截图。](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- 如果需要将组织中的其他人员添加到 Azure 订阅，请参阅 [Azure 门户中的访问管理入门](../active-directory/role-based-access-control-what-is.md)。 
- 如果需要添加管理员以帮助管理订阅，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

## <a id="more-about-subs">有关 Azure 和 Office 365 订阅的详细信息</a>
Office 365 和 Azure 使用 Azure AD 服务管理用户和订阅。 Azure 目录类似于一个容器，可在其中对用户和订阅分组。 若要将相同用户帐户用于 Azure 和 Office 365 订阅，需确保这些订阅是在同一目录中创建的。 请注意以下几点：

* 在目录下创建订阅。
* 用户属于目录。
* 订阅位于创建订阅的用户的目录中。 因此 Office 365 订阅被捆绑到与 Azure 订阅相同的帐户。
* Azure 订阅归目录中的各个用户所有。
* Office 365 订阅归目录本身所有。 在目录中具有相应权限的用户可管理这些订阅。

![显示目录、用户和订阅关系的屏幕截图。](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

有关详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。 
