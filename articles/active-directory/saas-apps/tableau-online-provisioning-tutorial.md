---
title: 教程：使用 Azure Active Directory 为 Tableau Online 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消预配用户帐户添加到 Tableau Online。
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123748"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教程：为 Tableau Online 配置自动用户预配

本教程演示了在 Tableau Online 和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消预配的用户和组添加到 Tableau Online 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用，它的工作原理，以及常见问题的信息，请参阅[自动用户预配和取消预配到软件作为-服务 (SaaS) 应用程序与 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必备组件

在本教程中所述的方案假定你拥有：

*   Azure AD 租户。
*   一个[Tableau Online 租户](https://www.tableau.com/)。
*   Tableau Online 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。 此 API 可供 Tableau Online 的开发人员。

## <a name="add-tableau-online-from-the-azure-marketplace"></a>从 Azure Marketplace 中添加 Tableau Online
您配置自动用户预配 Azure AD 与 Tableau Online 之前，Tableau Online 通过 Azure Marketplace 添加到托管 SaaS 应用程序的列表。

若要从 Marketplace 中添加 Tableau Online，请执行以下步骤。

1. 在中[Azure 门户](https://portal.azure.com)，在左侧导航窗格中，选择**Azure Active Directory**。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Tableau Online** ，然后选择**Tableau Online**结果面板中。 若要添加该应用程序，请选择**添加**。

    ![结果列表中的“Tableau Online”](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>将用户分配到 Tableau Online

Azure Active Directory 使用称为的概念*分配*来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步的用户或组分配给 Azure AD 中的应用程序。

配置和启用自动用户预配之前，确定哪些用户或组在 Azure AD 中的需要对 Tableau Online 的访问。 若要将这些用户或组分配到 Tableau Online，请按照中的说明[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>将用户分配到 Tableau Online 的重要提示

*   我们建议将分配一个 Azure AD 用户与 Tableau Online 测试自动用户预配配置。 可以稍后分配其他用户或组。

*   当将用户分配到 Tableau Online 时，选择任何有效的特定于应用程序的角色，如果可用，在分配对话框中。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>配置自动用户预配到 Tableau Online

本部分将指导您完成配置 Azure AD 预配服务的步骤。 使用它来创建、 更新和禁用中 Tableau Online 基于在 Azure AD 中的用户或组分配用户或组。

> [!TIP]
> 此外可以启用基于 SAML 的单一登录 Tableau Online 的。 按照中的说明[Tableau Online 单一登录教程](tableauonline-tutorial.md)。 单一登录可以独立于配置自动用户预配，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>配置自动用户在 Azure AD 中预配为 Tableau Online

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序** > **的所有应用程序** > **Tableau Online**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Tableau Online”。

    ![应用程序列表中的 Tableau Online 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Tableau Online 的预配模式](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 下**管理员凭据**部分中，输入域、 管理员用户名、 管理员密码和 Tableau Online 帐户的内容的 URL:

   * 在中**域**框中，填写基于步骤 6 的子域。

   * 在中**管理员用户名**框中，填入 Clarizen 租户的管理员帐户的用户名。 例如 admin@contoso.com。

   * 在中**管理员密码**框中，填入管理员用户名所对应的管理员帐户的密码。

   * 在中**内容 URL**框中，填写基于步骤 6 的子域。

6. 在登录到管理帐户 Tableau Online 后，可以获取的值**域**并**内容 URL**从管理员页的 URL。

    * Tableau Online 帐户的“域”可从 URL 的以下部分复制：

        ![Tableau Online 域](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **内容 URL**在 Tableau Online 的帐户可以将复制此节中。 它是在帐户设置期间定义的值。 在此示例中，该值为“contoso”：

        ![Tableau Online 内容 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 你**域**可能不同于此处所示。

7. 步骤 5 中所示的框中填充后，选择**测试连接**以确保 Azure AD 可以连接到 Tableau Online。 如果连接失败，请确保你 Tableau Online 的帐户具有管理员权限，然后重试。

    ![Tableau Online 测试连接](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在中**通知电子邮件**框中，输入用户的电子邮件地址或组以接收预配错误通知。 选择**发生故障时发送电子邮件通知**复选框。

    ![Tableau Online 的通知电子邮件](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Tableau”。

    ![Tableau Online 用户同步](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. 查看从 Azure AD 同步到 Tableau Online 中的用户属性**属性映射**部分。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Tableau Online 匹配用户属性](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Tableau”。

    ![Tableau Online 的组的同步](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. 查看从 Azure AD 同步到 Tableau Online 中的组属性**属性映射**部分。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Tableau Online 的匹配组属性](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 若要配置范围筛选器，按照中的说明[作用域筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

15. 若要启用 Azure AD 中预配服务为 Tableau Online**设置**部分中，更改**预配状态**到**上**。

    ![Tableau Online 的预配状态](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. 定义的用户或组所需预配到 Tableau Online。 在中**设置**部分中，选择在所需的值**作用域**。

    ![Tableau Online 的作用域](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 准备好预配后，选择**保存**。

    ![保存的 tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此操作会启动初始同步所有用户或组中定义**作用域**中**设置**部分。 初始同步长，若要执行的时间比更高版本的同步。 只要 Azure AD 预配服务运行，它们会大约每隔 40 分钟。 

可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告。 报告描述了预配服务对 Tableau Online 的 Azure AD 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
