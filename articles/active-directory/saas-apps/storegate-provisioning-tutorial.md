---
title: 教程：为 Storegate 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Storegate 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 1769b46210c766adc876dc36bf4e646fb23a6823
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905298"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>教程：为 Storegate 配置自动用户预配

本教程的目的是演示要在 Storegate 和 Azure Active Directory （Azure AD）中执行的步骤，以配置 Azure AD 自动将用户和/或组预配到 Storegate 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Storegate 租户](https://www.storegate.com)
* 具有管理员权限的 Storegate 上的用户帐户。

## <a name="assign-users-to-storegate"></a>将用户分配到 Storegate

Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 Storegate。 确定后，可按照此处的说明将这些用户和/或组分配到 Storegate：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>将用户分配到 Storegate 的重要提示

* 建议将单个 Azure AD 用户分配到 Storegate 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Storegate 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-storegate-for-provisioning"></a>设置 Storegate 以进行预配

在将 Storegate 配置为 Azure AD 的自动用户预配之前，需要从 Storegate 检索一些设置信息。

1. 登录到[Storegate 管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，并通过单击右上角的 "用户" 图标导航到 "设置"，然后选择 "**帐户设置**"。

    ![Storegate 添加 SCIM](media/storegate-provisioning-tutorial/admin.png)

2. 在 "设置" 中，导航到 " **Team > 设置**"，并验证是否在 "**单一登录**" 部分中打开了切换开关。

    ![Storegate 设置](media/storegate-provisioning-tutorial/team.png)

    ![Storegate 切换按钮](media/storegate-provisioning-tutorial/sso.png)

3. 复制**租户 URL**和**令牌**。 这些值将分别在 Azure 门户中的 Storegate 应用程序的 "预配" 选项卡中输入到 "**租户 URL** " 和 "**机密令牌**" 字段中。 

    ![Storegate 创建令牌](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>从库中添加 Storegate

若要为 Storegate 配置自动用户预 Azure AD 配，需要将 Azure AD 应用程序库中的 Storegate 添加到托管的 SaaS 应用程序列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新建应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Storegate**"，在结果面板中选择 " **Storegate** "。 

    ![结果列表中的 Storegate](common/search-new-app.png)

5. 选择 "**注册 Storegate** " 按钮，该按钮会将你重定向到 Storegate 的登录页。 

    ![Storegate OIDC 添加](media/storegate-provisioning-tutorial/signup.png)

6.  登录到[Storegate 管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，并通过单击右上角的 "用户" 图标导航到 "设置"，然后选择 "**帐户设置**"。

    ![Storegate 登录](media/storegate-provisioning-tutorial/admin.png)

7. 在 "设置" 中，导航到 " **Team > 设置**"，然后在 "单一登录" 部分中单击 "切换开关"，这将启动同意流。 单击 "**激活**"。

    ![Storegate 团队](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate 激活](media/storegate-provisioning-tutorial/activate.png)

8. 由于 Storegate 是一个 OpenIDConnect 应用，因此请选择使用你的 Microsoft 工作帐户登录到 Storegate。

    ![Storegate OIDC 登录](media/storegate-provisioning-tutorial/login.png)

9. 身份验证成功后，接受同意页面的许可提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Storegate 帐户。

    ![Storegate OIDc 同意](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>配置 Storegate 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Storegate 中创建、更新和禁用用户和/或组。

> [!NOTE]
> 若要了解有关 Storegate 的 SCIM 终结点的详细信息，请参阅[此](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)。

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>在 Azure AD 中配置 Storegate 的自动用户预配

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **Storegate**"。

    ![应用程序列表中的 Storegate 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://dialpad.com/scim`。 输入先前从 Storegate 中的 "**机密令牌**" 中检索并保存的值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Storegate。 如果连接失败，请确保 Storegate 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Storegate**"。

    ![Storegate 用户映射](media/storegate-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Storegate 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Storegate 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Storegate 用户属性](media/storegate-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Storegate 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![设置状态切换开启](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Storegate 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![正在保存设置配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Storegate 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
