---
title: 教程：为 Elium 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Elium 以及取消其预配。
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058454"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教程：为 Elium 配置自动用户预配

本教程介绍如何配置 Elium 和 Azure Active Directory （Azure AD）以自动将用户或组预配到 Elium 和取消其预配。

> [!NOTE]
> 本教程介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 有关此服务的用途和工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览版中的 Azure 功能的通用使用条款，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假定你已具备以下先决条件：

* Azure AD 租户
* [Elium 租户](https://www.elium.com/pricing/)
* Elium 中具有管理员权限的用户帐户

## <a name="assigning-users-to-elium"></a>将用户分配到 Elium

Azure AD 使用称为 "*分配*" 的概念来确定哪些用户接收对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和组。

在配置和启用自动用户预配之前，决定 Azure AD 中哪些用户和组需要访问 Elium。 然后，按照向[企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的步骤，将这些用户和组分配给 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>将用户分配到 Elium 的重要提示 

建议将单个 Azure AD 用户分配到 Elium 以测试自动用户预配配置。 稍后可以分配更多用户和组。

将用户分配到 Elium 时，必须在 "分配" 对话框中选择有效的应用程序特定角色（如果有）。 将从设置中排除具有**默认访问**角色的用户。

## <a name="set-up-elium-for-provisioning"></a>设置 Elium 以进行预配

在将 Elium 配置为使用 Azure AD 进行自动用户预配之前，必须在 Elium 上为跨域标识管理（SCIM）预配启用系统。 执行以下步骤:

1. 登录到 Elium，并 > "**设置**" 中转到 **"我的配置文件"** 。

    ![Elium 中的 "设置" 菜单项](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角的 "**高级**" 下，选择 "**安全性**"。

    ![Elium 中的安全链接](media/Elium-provisioning-tutorial/security.png)

1. 复制 "**租户 URL** " 和 "**机密令牌**" 值。 稍后将在 Azure 门户的 Elium 应用程序的 "**预配**" 选项卡的相应字段中使用这些值。

    ![Elium 中的租户 URL 和机密令牌字段](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>从库中添加 Elium

若要将 Elium 配置为使用 Azure AD 进行自动用户预配，还必须将 Azure AD 应用程序库中的 Elium 添加到托管的软件即服务（SaaS）应用程序列表。 执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![Azure Active Directory 菜单项](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。

     ![Azure AD 企业应用程序 "边栏选项卡](common/enterprise-applications.png)

1. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**"。

    ![新应用程序链接](common/add-new-app.png)

1. 在搜索框中，键入 " **Elium**"，在结果列表中选择 " **Elium** "，然后选择 "**添加**" 以添加该应用程序。

    ![库搜索框](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>配置 Elium 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配来创建、更新和禁用 Elium 中的用户和组的步骤。

> [!TIP]
> 你还可以根据 " [Elium 单一登录" 教程](Elium-tutorial.md)中的说明，选择根据安全断言标记语言（SAML）启用 Elium 的单一登录。 你可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

若要在 Azure AD 中配置 Elium 的自动用户预配，请执行以下步骤：

1. 登录到[Azure 门户](https://portal.azure.com)，选择 "**企业应用程序**"，然后选择 "**所有应用程序**"。

    ![Azure AD 企业应用程序 "边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Elium”。

    !["企业应用程序" 边栏选项卡中的应用程序列表](common/all-applications.png)

1. 选择“预配”选项卡。

    !["企业应用程序" 边栏选项卡](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![设置模式的自动设置](common/provisioning-automatic.png)

1. 在 "**管理员凭据**" 部分的 "**租户 URL** " 字段中，键入 **\<l\>/scim/v2** "。 （ **L**是之前从 Elium 管理控制台中检索到的值。）同时，在 "**机密令牌**" 字段中键入 Elium**机密令牌**值。 最后，选择 "**测试连接**" 以验证 Azure AD 能否连接到 Elium。 如果连接失败，请确保 Elium 帐户具有管理员权限，然后重试。

    ![管理凭据中的租户 URL 和机密令牌字段](common/provisioning-testconnection-tenanturltoken.png)

1. 在 "**通知电子邮件**" 字段中，输入将接收设置错误通知的人员或组的电子邮件地址。 然后，选中 "**发生故障时发送电子邮件通知**" 复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 单击 **“保存”** 。

1. 在 "**映射**" 部分，选择 "**将 Azure Active Directory 用户同步到 Elium**"。

    ![将 Azure AD 用户映射的链接同步到 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Elium 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Elium 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

    ![Azure AD 和 Elium 之间的属性映射](media/Elium-provisioning-tutorial/userattribute.png)

1. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

1. 若要为 Elium 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态设置为 On](common/provisioning-toggle-on.png)

1. 通过在 "**设置**" 部分的 "**作用域**" 下拉列表框中选择所需的值，定义要预配到 Elium 的用户和组。

    !["设置作用域" 列表框](common/provisioning-scope.png)

1. 准备好进行预配时，请选择 "**保存**"。

    ![用于设置配置的 "保存" 按钮](common/provisioning-configuration-save.png)

此操作将启动 "**设置**" 部分的 "**范围**" 中定义的所有用户和组的初始同步。 此初始同步过程所用的时间比稍后同步时间长。 有关预配所需时间的详细信息，请参阅[预配用户](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)需要多长时间？。

使用 "**当前状态**" 部分监视进度并跟踪指向预配活动报告的链接。 "设置活动" 报表介绍了 Azure AD 预配服务对 Elium 执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户设置](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
