---
title: 教程：使用 Azure Active Directory 为 Tableau Online 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Tableau Online 和取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: v-wingf-msft
ms.openlocfilehash: bbee7f0e6022e2945563c102a851819734d52e77
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "40106978"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>教程：为 Tableau Online 配置自动用户预配

本教程的目的是演示将 Azure AD 配置为自动将用户和/或组预配到 Tableau Online 以及取消其预配时，需在 Tableau Online 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

*   Azure AD 租户
*   [Tableau Online 租户](https://www.tableau.com/)
*   在 Tableau Online 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于可供 Tableau Online 开发人员使用的 [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)。

## <a name="adding-tableau-online-from-the-gallery"></a>从库中添加 Tableau Online
在使用 Azure AD 为 Tableau Online 配置自动用户预配之前，需将 Tableau Online 从 Azure AD 应用程序库添加到托管 SaaS 应用程序的列表。

**若要从 Azure AD 应用程序库中添加 Tableau Online，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]

3. 若要添加 Tableau Online，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Tableau Online”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/AppSearch.png)

5. 在结果面板中，选择“Tableau Online”，然后单击“添加”按钮将 Tableau Online 添加到 SaaS 应用程序列表。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/AppSearchResults.png)

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-tableau-online"></a>将用户分配到 Tableau Online

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Tableau Online。 确定后，可以按照此处的说明将这些用户和/或组分配到 Tableau Online：

*   [向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>将用户分配到 Tableau Online 的重要提示

*   建议将单个 Azure AD 用户分配到 Tableau Online 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Tableau Online，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>配置 Tableau Online 的自动用户预配

本部分介绍如何配置 Azure AD 预配服务，以便根据 Azure AD 中的用户和/或组分配在 Tableau Online 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Tableau Online 单一登录教程](tableauonline-tutorial.md)中提供的说明为 Tableau Online 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>若要在 Azure AD 中为 Tableau Online 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Tableau Online。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/AppInstanceSearch.png)

3. 选择“预配”选项卡。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入 Tableau Online 帐户的“域”、“管理员用户名”、“管理员密码”和“内容 URL”：

    *   在“域”字段中，基于步骤 6 填充子域。

    *   在“管理员用户名”字段中，填入 Clarizen 租户的管理员帐户的用户名。 示例：admin@contoso.com。

    *   在“管理员密码”字段中，填入管理员用户名所对应的管理员帐户的密码。

    *   在“内容 URL”字段中，基于步骤 6 填充子域。

6. 登录到 Tableau Online 的管理帐户以后，即可从“管理员”页的 URL 中提取“域”和“内容 URL”的值。

    *   Tableau Online 帐户的“域”可从 URL 的以下部分复制：![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    *   Tableau Online 帐户的“内容 URL”可从此部分复制，是在帐户设置过程中定义的值。 在此示例中，该值为“contoso”：![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 你的**域**可能不同于此处显示的域。 


7. 填充步骤 5 中所示的字段后，请单击“测试连接”，确保 Azure AD 可以连接到 Tableau Online。 如果连接失败，请确保 Tableau Online 帐户具有管理员权限，然后重试。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

10. 单击“ **保存**”。

11. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Tableau”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/UserMappings.png)

12. 在“属性映射”部分中，查看从 Azure AD 同步到 Tableau Online 的用户属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

13. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Tableau”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

14. 在“属性映射”部分中，查看从 Azure AD 同步到 Tableau Online 的组属性。 选为“匹配”属性的特性用于匹配 Tableau Online 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

15. 若要配置范围筛选器，请参阅[范围筛选器教程](../active-directory-saas-scoping-filters.md)中提供的以下说明。

16. 若要为 Tableau Online 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

17. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Tableau Online 的用户和/或组。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

18. 已准备好预配时，单击“保存”。

    ![Tableau Online 预配](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Tableau Online 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
