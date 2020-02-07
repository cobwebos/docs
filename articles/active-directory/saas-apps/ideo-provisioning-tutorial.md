---
title: 教程：为 IDEO 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 IDEO 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057560"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>教程：为 IDEO 配置自动用户预配

本教程的目的是演示要在 IDEO 和 Azure Active Directory （Azure AD）中执行的步骤，以配置 Azure AD 自动将用户和/或组预配到 IDEO 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [IDEO 租户](https://www.shape.space/product/pricing)
* IDEO 上的用户帐户 |具有管理员权限的形状。

## <a name="assign-users-to-ideo"></a>将用户分配到 IDEO

Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 IDEO。 确定后，可按照此处的说明将这些用户和/或组分配到 IDEO：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>将用户分配到 IDEO 的重要提示

* 建议将单个 Azure AD 用户分配到 IDEO 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 IDEO 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-ideo-for-provisioning"></a>设置 IDEO 以进行预配

在将 IDEO 配置为 Azure AD 的自动用户预配之前，需要从 IDEO 检索一些设置信息。

1. 对于**密钥令牌**，请在 productsupport@ideo.com联系 IDEO 支持团队。 此值将在 Azure 门户的 IDEO 应用程序的 "预配" 选项卡的 "**机密令牌**" 字段中输入。 

## <a name="add-ideo-from-the-gallery"></a>从库中添加 IDEO

若要为 IDEO 配置自动用户预 Azure AD 配，需要将 Azure AD 应用程序库中的 IDEO 添加到托管的 SaaS 应用程序列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **IDEO**"，在结果面板中选择 " **IDEO** "。 

    ![结果列表中的 IDEO](common/search-new-app.png)

5. 选择 "**注册 IDEO** " 按钮，该按钮会将你重定向到 IDEO 的登录页。 

    ![IDEO OIDC 添加](media/ideo-provisioning-tutorial/signup.png)

6. 由于 IDEO 是一个 OpenIDConnect 应用，因此请选择使用你的 Microsoft 工作帐户登录到 IDEO。

    ![IDEO OIDC 登录](media/ideo-provisioning-tutorial/login.png)

7. 身份验证成功后，接受同意页面的许可提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 IDEO 帐户。

    ![IDEO OIDc 同意](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>配置 IDEO 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 IDEO 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>若要在 Azure AD 中配置 IDEO 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **IDEO**"。

    ![应用程序列表中的 IDEO 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://profile.ideo.com/api/scim/v2`。 输入在 "**机密令牌**" 中从 IDEO 支持团队检索的值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 IDEO。 如果连接失败，请确保 IDEO 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 IDEO**"。

    ![IDEO 用户映射](media/ideo-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 IDEO 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 IDEO 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![IDEO 用户属性](media/ideo-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 IDEO 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](media/ideo-provisioning-tutorial/groupmappings.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 IDEO 的用户和/或组。

    ![预配范围](media/ideo-provisioning-tutorial/groupattributes.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 IDEO 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)


