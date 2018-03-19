---
title: "教程：使用 Azure Active Directory 为 Clarizen 配置自动用户预配 | Microsoft Docs"
description: "了解如何配置 Azure Active Directory 来自动将用户帐户预配到 Asana 以及取消其预配。"
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 3e75ce17f2806e4cb66f90f233b265398cdd0878
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>教程：为 Clarizen 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Clarizen 以及取消其预配需在 Clarizen 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](./active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

*   Azure AD 租户
*   启用了[企业版计划](https://www.clarizen.com/product/pricing/)或更佳计划的 Clarizen 租户 
*   在 Clarizen 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于 [Clarizen API](https://api.clarizen.com/v2.0/services/)，该 API 可供拥有基本计划或更佳计划的 Clarizen 团队使用。

## <a name="adding-clarizen-from-the-gallery"></a>从库中添加 Clarizen
在使用 Azure AD 为 Clarizen 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Clarizen 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Clarizen，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]
    
3. 若要添加 Clarizen，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Clarizen”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. 在结果面板中，选择“Clarizen”，然后单击“添加”按钮将 Clarizen 添加到 SaaS 应用程序列表。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>将用户分配到 Clarizen

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。 

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Clarizen。 确定后，可以按照此处的说明将这些用户和/或组分配到 Clarizen：

*   [向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>有关将用户分配到 Clarizen 的重要提示

*   建议将单个 Azure AD 用户分配到 Clarizen 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Clarizen，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>配置 Clarizen 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Clarizen 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Clarizen 单一登录教程](active-directory-saas-clarizen-tutorial.md)中提供的说明为 Clarizen 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>若要在 Azure AD 中为 Clarizen 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Clarizen。
 
    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. 选择“预配”选项卡。
    
    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入 Clarizen 帐户的“域”、“管理员用户名”和“管理员密码”。 这些值的示例如下：

    *   在“管理员用户名”字段中，填入 Clarizen 租户的管理员帐户的用户名。 示例：admin@contoso.com。

    *   在“管理员密码”字段中，填入管理员用户名所对应的管理员帐户的密码。

    *   在“域”字段中，基于步骤 6 填充子域。
    
6. 根据 [Clarizen 的 Rest API 指南](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2)的“身份验证”部分中提到的步骤检索 Clarizen 帐户的 **serverLocation**。 在获取 serverLocation 时，使用下面突出显示的 URL 的子域来填充“域”字段。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. 填入步骤 5 中所示的字段后，单击“测试连接”以确保 Azure AD 可以连接到 Clarizen。 如果连接失败，请确保 Clarizen 帐户具有管理员权限，然后重试。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. 单击“ **保存**”。

10. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Clarizen”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Clarizen 的用户属性。 选为“匹配”属性的特性用于匹配 Clarizen 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Clarizen”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Clarizen 的组属性。 选为“匹配”属性的特性用于匹配 Clarizen 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](./active-directory-saas-scoping-filters.md)中提供的以下说明。

15. 若要为 Clarizen 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Clarizen 的用户和/或组。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. 已准备好预配时，单击“保存”。

    ![Clarizen 预配](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Clarizen 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](./active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
