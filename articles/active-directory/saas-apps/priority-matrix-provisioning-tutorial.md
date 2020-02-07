---
title: 教程：通过 Azure Active Directory 配置优先级矩阵以实现自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到优先级矩阵并取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063432"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>教程：为自动用户预配配置优先级矩阵

本教程的目的是演示要在优先级矩阵中执行的步骤，并 Azure Active Directory （Azure AD）将 Azure AD 配置为自动将用户和/或组预配到优先级矩阵并取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [优先级矩阵租户](https://appfluence.com/pricing/)
* 具有管理员权限的优先级矩阵上的用户帐户。

## <a name="assign-users-to-priority-matrix"></a>将用户分配到优先级矩阵

Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问优先级矩阵。 确定后，可按照此处的说明将这些用户和/或组分配到优先级矩阵：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>将用户分配到优先级矩阵的重要提示

* 建议将单个 Azure AD 用户分配到优先级矩阵来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到优先级矩阵时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-priority-matrix-for-provisioning"></a>设置优先级矩阵以便预配

在为 Azure AD 的自动用户预配配置优先级矩阵之前，你将需要从优先级矩阵检索一些设置信息。

1. 登录到[优先级矩阵管理控制台](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning)。

3. 单击优先级矩阵的**Oauth 登录令牌**

    ![优先级矩阵添加 SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. 单击 "**获取新令牌**" 按钮。 复制**令牌字符串**。 此值将在 Azure 门户中优先级矩阵应用程序的 "预配" 选项卡的 "**机密令牌**" 字段中输入。 

    ![优先级矩阵创建标记](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>从库中添加优先级矩阵

若要配置 Azure AD 的自动用户预配的优先级矩阵，需要将 Azure AD 应用程序库中的优先级矩阵添加到托管的 SaaS 应用程序列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Priority matrix**，在结果面板中选择 "**优先级矩阵**"。 

    ![结果列表中的优先级矩阵](common/search-new-app.png)

5. 选择 "**为优先级矩阵注册**" 按钮，该按钮会将您重定向到优先级矩阵的登录页。 

    ![优先级矩阵 OIDC 添加](media/priority-matrix-provisioning-tutorial/signup.png)

6. 作为优先级矩阵是 OpenIDConnect 应用，请选择使用 Microsoft 工作帐户登录到优先级矩阵。

    ![优先级矩阵 OIDC 登录](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. 身份验证成功后，接受同意页面的许可提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的优先级矩阵帐户。

    ![优先级矩阵 OIDc 同意](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>配置优先级矩阵的自动用户预配 

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配来创建、更新和禁用用户和/或组的步骤。

> [!NOTE]
> 若要了解有关优先级矩阵的 SCIM 终结点的详细信息，请参阅[用户预配和优先级矩阵](https://appfluence.com/help/article/user-provisioning/)。

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>为 Azure AD 中的优先级矩阵配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 "**优先级矩阵**"。

    ![应用程序列表中的优先级矩阵链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://sync.appfluence.com/scim/v2/`。 输入先前从**机密令牌**中的优先级矩阵检索并保存的值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到优先级矩阵。 如果连接失败，请确保你的优先级矩阵帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到优先级矩阵**"。

    ![优先级矩阵用户映射](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到优先级矩阵的用户属性。 选为 "**匹配**" 属性的属性用于匹配 "优先级" 矩阵中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![优先级矩阵用户属性](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为优先级矩阵启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到优先级矩阵的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务在优先级矩阵上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)


