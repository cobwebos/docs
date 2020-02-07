---
title: 教程：为 Netskope 管理员控制台配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Netskope 管理员控制台并取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061213"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>教程：为 Netskope 管理员控制台配置自动用户预配

本教程的目的是演示要在 Netskope 管理员控制台和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到 Netskope 管理员控制台。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Netskope 管理员控制台租户](https://www.netskope.com/)
* 管理员控制台 Netskope 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-netskope-administrator-console"></a>将用户分配到 Netskope 管理员控制台

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问 Netskope 管理员控制台。 确定后，可按照此处的说明将这些用户和/或组分配到 Netskope 管理员控制台：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>将用户分配到 Netskope 的重要提示管理员控制台

* 建议将单个 Azure AD 用户分配到 Netskope 管理员控制台，以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Netskope 管理员控制台时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>设置用于预配的 Netskope 管理员控制台

1. 登录到[Netskope 管理员控制台管理控制台](https://netskope.goskope.com/)。 导航到**Home > 设置**。

    ![Netskope 管理员控制台管理控制台](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  导航到 "**工具**"。 在 "**工具**" 菜单下，导航到 "**目录工具" > SCIM INTEGRATION**"。

    ![Netskope 管理员控制台工具](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope 管理员控制台添加 SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. 向下滚动，然后单击 "**添加令牌**" 按钮。 在 "**添加 OAuth 客户端名称**" 对话框中，提供**客户端名称**，并单击 "**保存**" 按钮。

    ![Netskope 管理员控制台添加令牌](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope 管理员控制台客户端名称](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  复制**SCIM 服务器 URL**和**令牌**。 这些值将分别在 Azure 门户的 Netskope 管理员控制台应用程序的 "预配" 选项卡中输入到 "租户 URL" 和 "机密令牌" 字段中。

    ![Netskope 管理员控制台创建令牌](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>从库中添加 Netskope 管理员控制台

在配置 Netskope 管理员控制台以通过 Azure AD 自动用户预配之前，需要将 Azure AD 应用程序库中的 Netskope 管理员控制台添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加 Netskope 管理员控制台，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Netskope 管理员控制台**，在" 结果 "面板中选择"**管理员控制台 Netskope** "，然后单击"**添加**"按钮添加该应用程序。

    ![结果列表中的 Netskope 管理员控制台](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>配置 Netskope 管理员控制台的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Netskope 管理员控制台中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择按照[Netskope 管理员控制台单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)中提供的说明为 Netskope 管理员控制台启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要了解有关 Netskope 管理员控制台的 SCIM 终结点的详细信息，请参阅[此](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)。

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>若要为 Netskope 中的管理员控制台配置自动用户预配 Azure AD：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Netskope 管理员控制台”。

    ![应用程序列表中的 Netskope 管理员控制台链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分的 "输入**SCIM 服务器 url** " 值 **。** 输入先前在**机密令牌**中检索的**令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Netskope 管理员控制台。 如果连接失败，请确保管理员控制台 Netskope 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Netskope 管理员控制台**"。

    ![Netskope 管理员控制台用户映射](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Netskope 管理员控制台的用户属性。 选为 "**匹配**" 属性的属性用于匹配 Netskope 管理员控制台中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Netskope 管理员控制台用户属性](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 Netskope 管理员控制台**"。

    ![Netskope 管理员控制台组映射](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Netskope 管理员控制台的组属性。 选为 "**匹配**" 属性的属性用于匹配 Netskope 管理员控制台中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Netskope 管理员控制台组属性](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Netskope 管理员控制台启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Netskope 管理员控制台的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Netskope 管理员控制台执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

