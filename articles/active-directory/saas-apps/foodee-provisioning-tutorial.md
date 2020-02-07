---
title: 教程：使用 Azure Active Directory 为自动用户预配配置 Foodee |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动预配用户帐户并将其取消预配到 Foodee。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057799"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教程：为 Foodee 配置自动用户预配

本文介绍如何在 Foodee 中配置 Azure Active Directory （Azure AD），并将 Azure AD 自动预配或取消预配用户或组到 Foodee。

> [!NOTE]
> 本文介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 若要了解此服务的作用及其工作原理，并获得常见问题的答案，请参阅[使用 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览版功能的 Azure 使用条款功能的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假定你满足以下先决条件：

* Azure AD 租户
* [Foodee 租户](https://www.food.ee/about/)
* Foodee 中具有管理员权限的用户帐户

## <a name="assign-users-to-foodee"></a>将用户分配到 Foodee 

Azure AD 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户或组需要访问 Foodee。 做出此决定后，可以按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户或组分配到 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>将用户分配到 Foodee 的重要提示 

分配用户时，请记住以下提示：

* 建议仅将一个 Azure AD 用户分配到 Foodee 以测试自动用户预配的配置。 稍后可以分配其他用户或组。

* 将用户分配到 Foodee 时，请在 "**分配**" 窗格中选择任何特定于应用程序的有效角色（如果可用）。 将从设置中排除具有*默认访问*角色的用户。

## <a name="set-up-foodee-for-provisioning"></a>设置 Foodee 以进行预配

使用 Azure AD 配置 Foodee 以实现自动用户预配之前，需要在 Foodee 中为跨域标识管理（SCIM）预配启用系统。

1. 登录到[Foodee](https://www.food.ee/login/)，然后选择你的租户 ID。

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. 在 "**企业门户**" 下，选择 "**单一登录**"。

    ![Foodee Enterprise Portal 左窗格菜单](media/Foodee-provisioning-tutorial/scim.png)

1. 复制 " **API 令牌**" 框中的值供以后使用。 你将在 Azure 门户的 Foodee 应用程序的 "**预配**" 选项卡的 "**机密令牌**" 框中输入它。

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>从库中添加 Foodee

若要使用 Azure AD 为自动用户预配配置 Foodee，需要将 Azure AD 应用程序库中的 Foodee 添加到托管 SaaS 应用程序列表。

若要从 Azure AD 应用程序库中添加 Foodee，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。

    ![Azure Active Directory 命令](common/select-azuread.png)

1. 选择“企业应用程序” **“所有应用程序”**  > 。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**"。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中，输入 " **Foodee**"，在结果窗格中选择 " **Foodee** "，然后选择 "**添加**" 以添加该应用程序。

    ![结果列表中的 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>配置 Foodee 的自动用户预配 

在本部分中，将配置 Azure AD 预配服务，以便基于 Azure AD 中的用户或组分配在 Foodee 中创建、更新和禁用用户或组。

> [!TIP]
> 还可以按照[Foodee 单一登录教程](Foodee-tutorial.md)中的说明，为 Foodee 启用基于 SAML 的单一登录。 你可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

通过执行以下操作，在 Azure AD 中配置 Foodee 的自动用户预配：

1. 在[Azure 门户](https://portal.azure.com)中，选择 "**企业应用程序**" > "**所有应用程序**"。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在**应用程序**列表中，选择 " **Foodee**"。

    ![应用程序列表中的 Foodee 链接](common/all-applications.png)

1. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

1. 在 "**预配模式**" 下拉列表中，选择 "**自动**"。

    ![设置选项卡](common/provisioning-automatic.png)

1. 在 "**管理员凭据**" 下，执行以下操作：

   a. 在 "**租户 URL** " 框中，输入之前检索到的**https：\//concierge.food.ee/scim/v2**值。

   b. 在 "**机密令牌**" 框中，输入之前检索到的**API 令牌**值。
   
   c. 若要确保 Azure AD 可以连接到 Foodee，请选择 "**测试连接**"。 如果连接失败，请确保 Foodee 帐户具有管理员权限，然后重试。

    ![测试连接链接](common/provisioning-testconnection-tenanturltoken.png)

1. 在 "**通知电子邮件**" 框中，输入应接收预配错误通知的人员或组的电子邮件地址，然后选中 "**发生故障时发送电子邮件通知**" 复选框。

    !["通知电子邮件" 文本框](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在 "**映射**" 下，选择 "**将 Azure Active Directory 用户同步到 Foodee**"。

    ![Foodee 用户映射](media/Foodee-provisioning-tutorial/usermapping.png)

1. 在 "**属性映射**" 下，查看从 Azure AD 同步到 Foodee 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Foodee 中的*用户帐户*以执行更新操作。 

    ![Foodee 用户映射](media/Foodee-provisioning-tutorial/userattribute.png)

1. 若要提交更改，请选择 "**保存**"。
1. 在 "**映射**" 下，选择 "**将 Azure Active Directory 组同步到 Foodee**"。

    ![Foodee 用户映射](media/Foodee-provisioning-tutorial/groupmapping.png)

1. 在 "**属性映射**" 下，查看从 Azure AD 同步到 Foodee 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Foodee 中的*组帐户*以执行更新操作。

    ![Foodee 用户映射](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 若要提交更改，请选择 "**保存**"。
1. 配置范围筛选器。 若要了解如何操作，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

1. 若要为 Foodee 启用 Azure AD 预配服务，请在 "**设置**" 部分中，将 "**预配状态**" 更改为 **"开**"。

    ![预配状态开关](common/provisioning-toggle-on.png)

1. 在 "**设置**" 下的 "**作用域**" 下拉列表中，定义要预配到 Foodee 的用户或组。

    !["设置作用域" 下拉列表](common/provisioning-scope.png)

1. 准备好进行预配时，请选择 "**保存**"。

    ![设置配置保存按钮](common/provisioning-configuration-save.png)

前面的操作将启动在 "**作用域**" 下拉列表中定义的用户或组的初始同步。 初始同步执行的时间比后续同步长。 有关详细信息，请参阅[预配用户需要多长时间？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

你可以使用 "**当前状态**" 部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务对 Foodee 执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
