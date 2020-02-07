---
title: 教程：使用 Azure Active Directory 为 Tableau Online 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Tableau Online 并取消其设置。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064199"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教程：为 Tableau Online 配置自动用户预配

本教程演示在 Tableau Online 中执行的步骤，并 Azure Active Directory （Azure AD）将 Azure AD 配置为自动将用户和组预配到 Tableau 和取消其设置为联机。

> [!NOTE]
> 本教程介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 有关此服务的用途、工作原理和常见问题的信息，请参阅[使用 Azure Active Directory 自动执行用户预配和取消预配到软件即服务（SaaS）应用程序](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你具有：

*   Azure AD 租户。
*   [Tableau Online 租户](https://www.tableau.com/)。
*   Tableau Online 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Tableau Online REST API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可用于 Tableau Online 开发人员。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>从 Azure Marketplace 添加 Tableau Online
在将 Tableau Online 配置为 Azure AD 的自动用户预配之前，请将 Azure Marketplace 中的 Tableau 联机添加到托管的 SaaS 应用程序列表。

若要从 Marketplace 中添加 Tableau Online，请执行以下步骤。

1. 在[Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择 " **Azure Active Directory**"。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Tableau online** "，然后从结果面板中选择 " **Tableau online** "。 若要添加应用程序，请选择 "**添加**"。

    ![结果列表中的“Tableau Online”](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>将用户分配到 Tableau Online

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，决定 Azure AD 中的哪些用户或组需要访问 Tableau Online。 若要将这些用户或组分配到 Tableau Online，请按照[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>将用户分配到 Tableau Online 的重要提示

*   建议你将单个 Azure AD 用户分配到 Tableau Online，以测试自动用户预配配置。 稍后可以分配其他用户或组。

*   将用户分配到 Tableau Online 时，请在 "分配" 对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>配置 Tableau Online 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它基于 Azure AD 中的用户或组分配在 Tableau Online 中创建、更新和禁用用户或组。

> [!TIP]
> 还可以为 Tableau Online 启用基于 SAML 的单一登录。 按照[Tableau Online 单一登录教程](tableauonline-tutorial.md)中的说明进行操作。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>在 Azure AD 中为 Tableau Online 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**" > "**所有应用程序**" > **Tableau Online**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Tableau Online”。

    ![应用程序列表中的 Tableau Online 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Tableau Online 预配模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 "**管理员凭据**" 部分下，输入 Tableau Online 帐户的域、管理员用户名、管理员密码和内容 URL：

   * 在 "**域**" 框中，根据步骤6填写子域。

   * 在 "**管理员用户名**" 框中，填写 Clarizen 租户的管理员帐户的用户名。 示例为 admin@contoso.com。

   * 在 "**管理员密码**" 框中，填写与管理员用户名对应的管理员帐户的密码。

   * 在 "**内容 URL** " 框中，根据步骤6填写子域。

6. 登录到 Tableau Online 的管理帐户后，你可以从 "管理" 页的 URL 中获取 "**域**和**内容 url** " 的值。

    * Tableau Online 帐户的“域”可从 URL 的以下部分复制：

        ![Tableau Online 域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 可以从此节复制 Tableau Online 帐户的**内容 URL** 。 它是在帐户设置过程中定义的值。 在此示例中，该值为“contoso”：

        ![Tableau Online 内容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 你的域可能不同于此处显示的**域**。

7. 填写步骤5中所示的框后，请选择 "**测试连接**" 以确保 Azure AD 可以联机连接到 Tableau。 如果连接失败，请确保 Tableau Online 帐户具有管理员权限，然后重试。

    ![Tableau Online 测试连接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 "**通知电子邮件**" 框中，输入要接收设置错误通知的个人或组的电子邮件地址。 选中 "**发生故障时发送电子邮件通知**" 复选框。

    ![Tableau Online 通知电子邮件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Tableau”。

    ![Tableau Online 用户同步](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Tableau Online 的用户属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 若要保存任何更改，请选择 "**保存**"。

    ![Tableau Online 匹配用户属性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Tableau”。

    ![Tableau Online 组同步](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Tableau Online 的组属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 若要保存任何更改，请选择 "**保存**"。

    ![Tableau Online 匹配组属性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

15. 若要为 Tableau Online 启用 Azure AD 预配服务，请在 "**设置**" 部分中，将 "**预配状态**" 更改为 **"打开**"。

    ![Tableau Online 预配状态](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定义要预配到 Tableau Online 的用户或组。 在 "**设置**" 部分的 "**范围**" 中选择所需的值。

    ![Tableau Online 作用域](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 准备好进行预配时，请选择 "**保存**"。

    ![Tableau Online 保存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此操作将启动 "**设置**" 部分的 "**范围**" 中定义的所有用户或组的初始同步。 初始同步执行的时间比后续同步长。 只要运行 Azure AD 预配服务，它们大约每40分钟发生一次。 

您可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接。 该报表介绍 Azure AD 预配服务在 Tableau Online 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
