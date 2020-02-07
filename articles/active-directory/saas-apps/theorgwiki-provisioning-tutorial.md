---
title: 教程：为 TheOrgWiki 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 TheOrgWiki 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063132"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>教程：为 TheOrgWiki 配置自动用户预配

本教程的目的是演示要在 TheOrgWiki 和 Azure Active Directory （Azure AD）中执行的步骤，以配置 Azure AD 自动将用户和/或组预配到 TheOrgWiki 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [OrgWiki 租户](https://www.theorgwiki.com/welcome/)。
* TheOrgWiki 中具有管理员权限的用户帐户。

## <a name="assign-users-to-theorgwiki"></a>将用户分配到 TheOrgWiki

Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 TheOrgWiki。 确定后，可按照此处的说明将这些用户和/或组分配到 TheOrgWiki：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>将用户分配到 TheOrgWiki 的重要提示

* 建议将单个 Azure AD 用户分配到 TheOrgWiki 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 TheOrgWiki 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-theorgwiki-for-provisioning"></a>设置 TheOrgWiki 以进行预配

将 TheOrgWiki 配置为使用 Azure AD 进行自动用户预配之前，需要在 TheOrgWiki 上启用 SCIM 设置。

1. 登录到[TheOrgWiki 管理控制台](https://www.theorgwiki.com/login/)。 单击 "**管理控制台**"。

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. 在管理员控制台中，单击 "**设置" 选项卡**。 

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. 导航到 "**服务帐户**"。

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. 单击 " **+ 服务帐户**"。 在 "**服务帐户类型**" 下，选择 "**基于令牌**"。 单击 **“保存”** 。

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  复制**活动标记**。 此值将在 Azure 门户的 TheOrgWiki 应用程序的 "预配" 选项卡的 "机密令牌" 字段中输入。
     
    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>从库中添加 TheOrgWiki

若要为 TheOrgWiki 配置自动用户预 Azure AD 配，需要将 Azure AD 应用程序库中的 TheOrgWiki 添加到托管的 SaaS 应用程序列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **TheOrgWiki**"，在结果面板中选择 " **TheOrgWiki** "。 

    ![结果列表中的 TheOrgWiki](common/search-new-app.png)

5. 选择 "**注册 TheOrgWiki** " 按钮，该按钮会将你重定向到 TheOrgWiki 的登录页。 

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  在右上角，选择 "**登录**"。

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. 由于 TheOrgWiki 是一个 OpenIDConnect 应用，因此请选择使用你的 Microsoft 工作帐户登录到 OrgWiki。

    ![TheOrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. 身份验证成功后，应用程序将自动添加到你的租户，你会被重定向到你的 TheOrgWiki 帐户。

    ![OrgWiki 添加 SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>配置 TheOrgWiki 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 TheOrgWiki 中创建、更新和禁用用户和/或组。


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>若要在 Azure AD 中配置 TheOrgWiki 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **TheOrgWiki**"。

    ![应用程序列表中的 OrgWiki 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/`。 

    示例： `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> 仅可在 TheOrgWiki 的初始注册过程中设置**子域值**。
 
6. 在 "**机密令牌**" 字段中输入 TheOrgWiki 之前检索到的令牌值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 TheOrgWiki。 如果连接失败，请确保 TheOrgWiki 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

8. 单击 **“保存”** 。

9. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 TheOrgWiki**"。

    ![TheOrgWiki 用户映射](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 TheOrgWiki 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 TheOrgWiki 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![TheOrgWiki 用户属性](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

12. 若要为 TheOrgWiki 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

13. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 OrgWiki 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

14. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长。 有关设置用户和/或组所需的时间的详细信息，请参阅[预配用户](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)需要多长时间。

你可以使用 "**当前状态**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 TheOrgWiki 执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户设置](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报表](../app-provisioning/check-status-user-account-provisioning.md)。