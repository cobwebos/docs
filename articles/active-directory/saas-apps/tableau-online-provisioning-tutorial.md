---
title: 教程：使用 Azure Active Directory 为 Tableau Online 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure 活动目录配置为自动预配和取消将用户帐户预配到 Tableau Online。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064199"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教程：为 Tableau Online 配置自动用户预配

本教程演示了在 Tableau 联机目录和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和组预配到 Tableau Online。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用、工作方式以及常见问题的信息，请参阅[使用 Azure 活动目录 自动预配和取消预配软件即服务 （SaaS） 应用程序](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定您具有：

*   Azure AD 租户。
*   [Tableau 在线租户](https://www.tableau.com/)。
*   Tableau Online 中的具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Tableau 在线休息 API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可供 Tableau 在线开发人员使用。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>从 Azure 应用商店联机添加 Tableau
在为使用 Azure AD 自动用户预配配置之前，请将 Tableau Online 从 Azure 应用商店添加到托管 SaaS 应用程序列表中。

要从应用商店添加 Tableau 联机，请按照以下步骤操作。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧的导航窗格中，选择**Azure 活动目录**。

    ![Azure 活动目录图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”****。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Tableau 联机**，并从结果面板中选择**Tableau 联机**。 要添加应用程序，请选择"**添加**"。

    ![结果列表中的“Tableau Online”](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>将用户分配到 Tableau 联机

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问 Tableau Online。 要将这些用户或组分配给 Tableau Online，请按照将[用户或组分配给企业应用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>将用户分配到 Tableau Online 的重要提示

*   我们建议您将单个 Azure AD 用户分配给 Tableau Online 以测试自动用户预配配置。 您可以稍后分配其他用户或组。

*   将用户分配给 Tableau Online 时，请在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>配置自动用户预配到 Tableau 在线

本节将指导您完成配置 Azure AD 预配服务的步骤。 使用它根据 Azure AD 中的用户或组分配在 Tableau Online 中创建、更新和禁用用户或组。

> [!TIP]
> 您还可以为 Tableau Online 启用基于 SAML 的单一登录。 按照[Tableau 在线单一登录教程](tableauonline-tutorial.md)中的说明进行操作。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>在 Azure AD 中为 Tableau 联机配置自动用户预配

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序** > **所有应用程序** > **Tableau 在线**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Tableau Online”****。

    ![应用程序列表中的 Tableau 联机链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 将**预配模式**设置为 **"自动**"。

    ![Tableau 在线预配模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 **"管理员凭据"** 部分下，输入 Tableau Online 帐户的域、管理员用户名、管理员密码和内容 URL：

   * 在 **"域"** 框中，根据步骤 6 填写子域。

   * 在 **"管理员用户名"** 框中，填写 Clarizen 租户上管理员帐户的用户名。 示例为 admin@contoso.com。

   * 在 **"管理员密码"** 框中，填写与管理员用户名对应的管理员帐户的密码。

   * 在 **"内容 URL"** 框中，根据步骤 6 填写子域。

6. 登录到 Tableau Online 的管理帐户后，可以从管理页面的 URL 获取**域**和**内容 URL**的值。

    * Tableau Online 帐户的“域”可从 URL 的以下部分复制****：

        ![Tableau 在线域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 可以从此部分复制 Tableau Online 帐户**的内容 URL。** 它是在帐户设置期间定义的值。 在此示例中，该值为“contoso”：

        ![Tableau 在线内容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 您的**域**可能与此处显示的域不同。

7. 填写步骤 5 中所示的框后，选择 **"测试连接**"以确保 Azure AD 可以连接到 Tableau Online。 如果连接失败，请确保您的 Tableau Online 帐户具有管理员权限，然后重试。

    ![Tableau 在线测试连接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在 **"通知电子邮件"** 框中，输入接收预配错误通知的个人或组的电子邮件地址。 选中"**在发生故障时发送电子邮件通知**"复选框。

    ![Tableau 在线通知电子邮件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 选择“保存”。****

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Tableau”********。

    ![Tableau 在线用户同步](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 在 **"属性映射**"部分中查看从 Azure AD 同步到 Tableau 联机的用户属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作****。 要保存任何更改，请选择"**保存**"。

    ![Tableau 在线匹配用户属性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Tableau”********。

    ![Tableau 联机组同步](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 在 **"属性映射**"部分中查看从 Azure AD 同步到 Tableau 联机的组属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作****。 要保存任何更改，请选择"**保存**"。

    ![Tableau 联机匹配组属性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 要配置范围筛选器，请按照[范围界定筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

15. 要为 Tableau Online 启用 Azure AD 预配服务，在 **"设置"** 部分中，将**预配状态**更改为**On**。

    ![Tableau 在线预配状态](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定义要预配到 Tableau Online 的用户或组。 在 **"设置"** 部分中，在 **"范围**"中选择所需的值。

    ![Tableau 在线范围](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 准备好预配时，请选择"**保存**"。

    ![Tableau 在线保存](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户或组的初始同步。 初始同步执行的时间比以后同步长。 只要 Azure AD 预配服务运行，它们大约每 40 分钟发生一次。 

您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Tableau Online 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
