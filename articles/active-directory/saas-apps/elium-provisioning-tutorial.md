---
title: 教程：使用 Azure 活动目录配置 Elium 以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 Elium 预配用户帐户。
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058454"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Elium

本教程演示如何配置 Elium 和 Azure 活动目录 （Azure AD） 以自动预配和取消向 Elium 预配用户或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用及其工作方式以及常见问题的重要详细信息，请参阅[使用 Azure 活动目录 自动预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览版中 Azure 功能的一般使用条款，请参阅[Microsoft Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假定您已经具备以下先决条件：

* Azure AD 租户
* [Elium 租户](https://www.elium.com/pricing/)
* 具有管理员权限的 Elium 中的用户帐户

## <a name="assigning-users-to-elium"></a>将用户分配给 Elium

Azure AD 使用称为*分配*的概念来确定哪些用户有权访问选定的应用。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户和组需要访问 Elium。 然后，按照"[将用户或组分配到企业应用](../manage-apps/assign-user-or-group-access-portal.md)"中的步骤将这些用户和组分配给 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>将用户分配给 Elium 的重要提示 

我们建议您将单个 Azure AD 用户分配给 Elium 以测试自动用户预配配置。 以后可以分配更多的用户和组。

将用户分配给 Elium 时，必须在分配对话框中选择有效的特定于应用程序的角色（如果有可用）。 具有**默认访问权限**角色的用户将从预配中排除。

## <a name="set-up-elium-for-provisioning"></a>设置用于预配的 Elium

在使用 Azure AD 配置 Elium 以自动预配用户之前，必须在 Elium 上启用跨域标识管理系统 （SCIM） 预配。 执行以下步骤:

1. 登录到 Elium 并转到**我的个人资料** > **设置**。

    ![设置菜单项在 Elium](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角，在**ADVANCED**下，选择 **"安全**"。

    ![Elium 中的安全链接](media/Elium-provisioning-tutorial/security.png)

1. 复制**租户 URL**和**机密令牌**值。 稍后将在 Azure 门户中 Elium 应用程序的**预配**选项卡中的相应字段中使用这些值。

    ![Elium 中的租户 URL 和秘密令牌字段](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>从库中添加 Elium

要配置 Elium 以使用 Azure AD 进行自动用户预配，还必须将 Azure AD 应用程序库中的 Elium 添加到托管软件即服务 （SaaS） 应用程序列表中。 执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)中，在左侧导航面板中，选择**Azure 活动目录**。

    ![Azure 活动目录菜单项](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。********

     ![Azure AD 企业应用程序边栏选项卡](common/enterprise-applications.png)

1. 要添加新应用程序，请在窗格顶部选择 **"新建应用程序**"。

    ![新的应用程序链接](common/add-new-app.png)

1. 在搜索框中，键入**Elium**，在结果列表中选择**Elium，** 然后选择 **"添加"** 以添加应用程序。

    ![库搜索框](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>配置自动用户预配到 Elium

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和组分配在 Elium 中创建、更新和禁用用户和组的步骤。

> [!TIP]
> 您也可以按照[Elium 单登录教程](Elium-tutorial.md)中的说明，选择基于安全断言标记语言 （SAML） 为 Elium 启用单一登录。 您可以独立于自动用户预配配置单一登录，尽管这两个功能相辅相成。

要在 Azure AD 中配置 Elium 的自动用户预配，请按照以下步骤操作：

1. 登录到 Azure[门户](https://portal.azure.com)，选择**企业应用程序**，然后选择**所有应用程序**。

    ![Azure AD 企业应用程序边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Elium”****。

    ![企业应用程序边栏选项卡中的应用程序列表](common/all-applications.png)

1. 选择“预配”**** 选项卡。

    ![企业应用程序边栏选项卡中的预配选项卡](common/provisioning.png)

1. 将**预配模式**设置为 **"自动**"。

    ![预配模式的自动设置](common/provisioning-automatic.png)

1. 在 **"管理凭据"** 部分中，在 **"租户 URL"** 字段中键入**\<租户URL\>/scim/v2。** （**租户URL**是较早从 Elium 管理控制台检索的值。还在 **"机密令牌"** 字段中键入 Elium**机密令牌**值。 最后，选择 **"测试连接**"以验证 Azure AD 是否可以连接到 Elium。 如果连接失败，请确保您的 Elium 帐户具有管理员权限，然后重试。

    ![管理员凭据中的租户 URL 和秘密令牌字段](common/provisioning-testconnection-tenanturltoken.png)

1. 在 **"通知电子邮件"** 字段中，输入将接收预配错误通知的个人或组的电子邮件地址。 然后，选择"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 单击“保存”。****

1. 在 **"映射"** 部分中，选择**将 Azure 活动目录用户同步到 Elium**。

    ![同步链接，将 Azure AD 用户映射到 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在**属性映射**部分中查看从 Azure AD 同步到 Elium 的用户属性。 选择为 **"匹配属性"** 的属性用于与 Elium 中的用户帐户匹配以进行更新操作。 选择“保存”，提交所有更改****。

    ![Azure AD 和 Elium 之间的属性映射](media/Elium-provisioning-tutorial/userattribute.png)

1. 要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

1. 要启用 Elium 的 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态设置为"打开"](common/provisioning-toggle-on.png)

1. 通过在 **"设置"** 部分的 **"范围**"下拉列表框中选择所需的值，定义要预配到 Elium 的用户和组。

    ![预配范围列表框](common/provisioning-scope.png)

1. 准备好预配时，请选择"**保存**"。

    ![保存用于预配配置的按钮](common/provisioning-configuration-save.png)

此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户和组的初始同步。 此初始同步过程所需的时间比以后的同步长。 有关预配所需的时间的详细信息，请参阅[预配用户需要多长时间？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

使用 **"当前状态**"部分监视进度并关注指向预配活动报告的链接。 预配活动报告描述 Azure AD 预配服务在 Elium 上执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要读取 Azure AD 预配日志，请参阅[报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
