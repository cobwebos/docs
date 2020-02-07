---
title: 教程：通过 Azure Active Directory 为自动用户预配配置动态信号 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到动态信号和取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2ec91d42dff8f3a1fc4b036aa1c3ec77faf6a0fc
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058036"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>教程：为自动用户预配配置动态信号

本教程的目的是演示在动态信号中执行的步骤，并 Azure Active Directory （Azure AD）将 Azure AD 配置为自动将用户和/或组预配到动态信号，并取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [动态信号租户](https://dynamicsignal.com/)
* 具有管理员权限的动态信号中的用户帐户。

## <a name="add-dynamic-signal-from-the-gallery"></a>从库中添加动态信号

在为自动用户预配配置 Azure AD 的动态信号之前，需要将 Azure AD 应用程序库中的动态信号添加到托管 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加动态信号，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 "**动态信号**"，在结果面板中选择 "**动态信号**"，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Dynamic Signal](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>将用户分配到动态信号

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问动态信号。 确定后，可按照此处的说明将这些用户和/或组分配给动态信号：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>将用户分配到动态信号的重要提示

* 建议为单个 Azure AD 用户分配动态信号来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到动态信号时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>配置动态信号的自动用户预配 

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配来创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 你还可以选择根据[动态信号单一登录教程](dynamicsignal-tutorial.md)中提供的说明为动态信号启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>在 Azure AD 中为动态信号配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Dynamic Signal”。

    ![应用程序列表中的 Dynamic Signal 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下，输入你的动态信号帐户的**租户 URL**和**机密令牌**，如步骤6中所述。

6. 在 "动态信号管理" 控制台中，导航到 "**管理员 > 高级 > API**"。

    ![动态信号预配](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    将**SCIM API url**复制到**租户 url**。 单击 "**生成新令牌**" 生成**持有者令牌**，并将值复制到 "**机密令牌**"。

    ![动态信号预配](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. 填充步骤5中所示的字段后，请单击 "**测试连接**" 以确保 Azure AD 可以连接到动态信号。 如果连接失败，请确保动态信号帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击 **“保存”** 。

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到动态信号**"。

    ![动态信号用户映射](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到动态信号的用户属性。 选为 "**匹配**" 属性的属性用于匹配动态信号中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![动态信号用户属性](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为动态信号启用 Azure AD 预配服务，请在 "设置" 部分中将**设置** **状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到动态信号的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对动态信号执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* 动态信号不支持 Azure AD 的永久用户删除。 若要永久删除用户的动态信号，必须通过动态信号管理控制台 UI 执行此操作。 
* 动态信号当前不支持组。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

