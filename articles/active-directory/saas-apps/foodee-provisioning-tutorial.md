---
title: 教程：使用 Azure 活动目录配置 Foode 以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 Foodee 预配用户帐户。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057799"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Foodee

本文介绍如何在 Foodee 和 Azure AD 中配置 Azure 活动目录 （Azure AD） 以自动预配或取消向 Foodee 预配用户或组。

> [!NOTE]
> 本文介绍了在 Azure AD 用户预配服务之上构建的连接器。 要了解此服务的作用及其工作原理，并获取常见问题的解答，请参阅[使用 Azure 活动目录 自动预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览功能的 Azure 使用条款功能的详细信息，请访问 Microsoft Azure[预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假定您已满足以下先决条件：

* Azure AD 租户
* [美食家租户](https://www.food.ee/about/)
* 具有管理员权限的 Foodee 中的用户帐户

## <a name="assign-users-to-foodee"></a>将用户分配给 Foodee 

Azure AD 使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户或组需要访问 Foodee。 做出此确定后，您可以按照将[用户或组分配给企业应用](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户或组分配给 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>将用户分配给 Foode 的重要提示 

在分配用户时，请记住以下提示：

* 我们建议您只向 Foodee 分配单个 Azure AD 用户，以测试自动用户预配的配置。 您可以稍后分配其他用户或组。

* 将用户分配给 Foodee 时，请在 **"分配"** 窗格中选择任何有效的特定于应用程序的角色（如果可用）。 具有*默认访问权限*角色的用户将从预配中排除。

## <a name="set-up-foodee-for-provisioning"></a>设置用于预配的食品

在使用 Azure AD 为自动用户预配配置 Foode 之前，需要在 Foodee 中启用跨域标识管理系统 （SCIM） 预配系统。

1. 登录到[Foodee，](https://www.food.ee/login/)然后选择您的租户 ID。

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. 在 **"企业门户**"下，选择 **"单一登录**"。

    ![美食企业门户左窗格菜单](media/Foodee-provisioning-tutorial/scim.png)

1. 复制**API 令牌**框中的值，以便以后使用。 您将在 Azure 门户中"提供"应用程序的 **"预配**"选项卡中的 **"机密令牌"** 框中输入它。

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>从库中添加美食家

要使用 Azure AD 配置 Foodee 以自动预配用户，需要将从 Azure AD 应用程序库中的 Foodee 添加到托管 SaaS 应用程序列表中。

要从 Azure AD 应用程序库添加 Foodee，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”****。

    ![Azure 活动目录命令](common/select-azuread.png)

1. 选择**企业应用程序** > **所有应用程序**。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 要添加新应用程序，请在窗格顶部选择 **"新建应用程序**"。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中，在结果窗格中选择 **"Foodee"，** 然后选择 **"添加**"以添加应用程序。 **Foodee**

    ![结果列表中的美食家](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>配置对 Foodee 的自动用户预配 

在本节中，您将 Azure AD 预配服务配置为根据 Azure AD 中的用户或组分配创建、更新和禁用 Foode 中的用户或组。

> [!TIP]
> 您还可以按照[Foode 单一登录教程](Foodee-tutorial.md)中的说明为 Foode 启用基于 SAML 的单一登录。 您可以独立于自动用户预配配置单一登录，尽管这两个功能相辅相成。

通过执行以下操作，在 Azure AD 中为 Foode 配置自动用户预配：

1. 在[Azure 门户](https://portal.azure.com)中，选择**企业应用程序** > **所有应用程序**。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在 **"应用程序**"列表中，选择 **"美食家**"。

    !["应用程序"列表中的美食人链接](common/all-applications.png)

1. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

1. 在**预配模式**下拉列表中，选择 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

1. 在 **"管理员凭据"** 下，执行以下操作：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 **"租户 URL"** 框中，输入之前检索的**\/https：/concierge.food.ee/scim/v2**值。

   b.保留“数据库类型”设置，即设置为“共享”。 在 **"秘密令牌"** 框中，输入之前检索的**API 令牌**值。
   
   c. 为确保 Azure AD 可以连接到 Foodee，请选择 **"测试连接**"。 如果连接失败，请确保您的 Foodee 帐户具有管理员权限，然后重试。

    ![测试连接链接](common/provisioning-testconnection-tenanturltoken.png)

1. 在 **"通知电子邮件**"框中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选择"在**发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件文本框](common/provisioning-notification-email.png)

1. 选择“保存”。****

1. 在 **"映射**"下，选择**将 Azure 活动目录用户同步到 Foode。**

    ![食品用户映射](media/Foodee-provisioning-tutorial/usermapping.png)

1. 在**属性映射**下，查看从 Azure AD 同步到 Foodee 的用户属性。 选择为 **"匹配属性"** 的属性用于与 Foodee 中的*用户帐户*匹配以进行更新操作。 

    ![食品用户映射](media/Foodee-provisioning-tutorial/userattribute.png)

1. 要提交更改，请选择"**保存**"。
1. 在 **"映射**"下，选择**将 Azure 活动目录组同步到 Foode。**

    ![食品用户映射](media/Foodee-provisioning-tutorial/groupmapping.png)

1. 在**属性映射**下，查看从 Azure AD 同步到 Foodee 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Foodee 中的*组帐户*以进行更新操作。

    ![食品用户映射](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 要提交更改，请选择"**保存**"。
1. 配置范围筛选器。 要了解如何操作，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

1. 要为 Foode 启用 Azure AD 预配服务，在 **"设置"** 部分中，将**预配状态**更改为**On**。

    ![预配状态开关](common/provisioning-toggle-on.png)

1. 在 **"设置**"下**Scope**拉列表中，定义要预配到 Foodee 的用户或组。

    ![预配范围下拉列表](common/provisioning-scope.png)

1. 准备好预配时，请选择"**保存**"。

    ![预配配置保存按钮](common/provisioning-configuration-save.png)

前面的操作开始您在 **"范围**"下拉列表中定义的用户或组的初始同步。 初始同步执行的时间比后续同步长。 有关详细信息，请参阅[预配用户需要多长时间？。](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

您可以使用 **"当前状态"** 部分监视进度并关注指向预配活动报告的链接。 该报表描述 Azure AD 预配服务在 Foode 上执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要读取 Azure AD 预配日志，请参阅[报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
