---
title: 教程：使用 Azure Active Directory 为 xMatters OnDemand 配置自动用户预配功能 | Microsoft Docs
description: 了解如何配置 Azure Active Directory，使其将用户帐户自动预配和取消预配到 xMatters OnDemand。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: b5038d176c302a4a2f75e2dd5235ea8f4789f10b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222628"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>教程：配置 xMatters OnDemand 进行自动用户预配

本教程旨在演示相关步骤，引导用户在 xMatters OnDemand 和 Azure Active Directory (Azure AD) 中配置 Azure AD，以使其将用户帐户自动预配和取消预配到 xMatters OnDemand。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

*   Azure AD 租户
*   xMatters OnDemand 租户，该租户已启用[入门](https://www.xmatters.com/pricing)计划或更高级的计划 
*   xMatters OnDemand 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成功能依赖于 [xMatters OnDemand API](https://help.xmatters.com/xmAPI)，而后者可供“入门”计划或更高级计划中的 xMatters OnDemand 团队使用。

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>从库添加 xMatters OnDemand

在配置 xMatters OnDemand，使其通过 Azure AD 自动预配用户之前，需将 Azure AD 应用程序库中的 xMatters OnDemand 添加到托管的 SaaS 应用程序列表。

**若要添加 Azure AD 应用程序库中的 xMatters OnDemand，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]
    
3. 若要添加 xMatters OnDemand，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“xMatters OnDemand”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. 在结果面板中，选择“xMatters OnDemand”，然后单击“添加”按钮，将 xMatters OnDemand 添加到 SaaS 应用程序列表。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>将用户分配到 xMatters OnDemand

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配功能之前，应确定 Azure AD 中的哪些用户和/或组需要访问 xMatters OnDemand。 确定后，可按照下方的说明将这些用户和/或组分配到 xMatters OnDemand：

*   [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>有关将用户分配到 xMatters OnDemand 的重要提示

*   建议将单个 Azure AD 用户分配到 xMatters OnDemand 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 xMatters OnDemand，必须在分配对话框中选择应用程序特定的任意有效角色（若有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>配置 xMatters OnDemand 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务，进而基于 Azure AD 中的用户和/或组分配在 xMatters OnDemand 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [xMatters OnDemand 单一登录教程](xmatters-ondemand-tutorial.md)中的说明为 xMatters OnDemand 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>若要在 Azure AD 中为 xMatters OnDemand 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 在 SaaS 应用程序列表中选择 xMatters OnDemand。
 
    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. 选择“预配”选项卡。
    
    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入 xMatters OnDemand 帐户的管理员用户名、管理员密码和域。

    *   在“管理员用户名”字段中，填入 xMatters OnDemand 租户上的管理员帐户的用户名。 示例：admin@contoso.com。

    *   在“管理员密码”字段中，填入管理员用户名所对应的密码。

    *   在“域”字段中，填入 xMatters OnDemand 租户的子域。
    示例：对于租户 URL 为 https://my-tenant.xmatters.com 的帐户，子域为 **my-tenant**。

6. 填写步骤 5 中所示的字段后，请单击“测试连接”，确保 Azure AD 可连接到 xMatters OnDemand。 如果连接失败，请确保 xMatters OnDemand 帐户具有管理员权限，然后重试。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. 单击“ **保存**”。

9. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 xMatters OnDemand”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. 在“属性映射”部分中，查看从 Azure AD 同步到 xMatters OnDemand 的用户属性。 使用被选为“匹配”属性的特性与 xMatters OnDemand 中的用户帐户进行匹配，从而执行更新操作。 选择“保存”按钮以提交任何更改。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 xMatters OnDemand”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. 在“属性映射”部分中，查看从 Azure AD 同步到 xMatters OnDemand 的组属性。 使用被选为“匹配”属性的特性与 xMatters OnDemand 中的组进行匹配，从而执行更新操作。 选择“保存”按钮以提交任何更改。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. 若要配置范围筛选器，请参阅[范围筛选器教程](../active-directory-saas-scoping-filters.md)中提供的以下说明。

14. 若要为 xMatters OnDemand 启用 Azure AD 预配服务，请将“设置”部分中的“预配状态”改为“开”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 xMatters OnDemand 的用户和/或组。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. 已准备好预配时，单击“保存”。

    ![xMatters OnDemand 预配](./media/xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 xMatters OnDemand 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
