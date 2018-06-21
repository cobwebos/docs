---
title: 教程：在 Azure Active Directory 中配置 Zendesk 实现自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Zendesk 和取消其预配。
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
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 6ba2fd9ee81b8551cc2a267cdc9767f47fe27456
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229047"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教程：配置 Zendesk 实现自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Zendesk 以及取消其预配需在 Zendesk 和 Azure Active Directory (Azure AD) 中执行的步骤。 

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](./../active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

*   Azure AD 租户
*   启用了[企业计划](https://www.zendesk.com/product/pricing/)或更佳计划的 Zendesk 租户 
*   Zendesk 中具有管理员权限的用户帐户 

> [!NOTE]
> Azure AD 预配集成依赖于 [Zendesk REST API](https://developer.zendesk.com/rest_api/docs/core/introduction)，其可供拥有企业计划或更佳计划的 Zendesk 团队使用。

## <a name="adding-zendesk-from-the-gallery"></a>从库中添加 Zendesk
在使用 Azure AD 为 Zendesk 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Zendesk 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Zendesk，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]
    
3. 若要添加 Zendesk，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Zendesk”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. 在结果面板中，选择“Zendesk”，然后单击“添加”按钮将 Zendesk 添加到 SaaS 应用程序列表。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>将用户分配到 Zendesk

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。 

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Zendesk。 确定后，可以按照此处的说明将这些用户和/或组分配到 Zendesk：

*   [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>将用户分配到 Zendesk 的重要提示

*   建议将单个 Azure AD 用户分配到 Zendesk 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Zendesk，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>配置 Zendesk 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Zendesk 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Zendesk 单一登录教程](zendesk-tutorial.md)中提供的说明为 Zendesk 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>若要在 Azure AD 中为 Zendesk 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Zendesk。
 
    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. 选择“预配”选项卡。
    
    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 将“预配模式”设置为“自动”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 在“管理员凭据”部分下，输入 Zendesk 帐户的“管理员用户名”、“机密令牌”和“域”。 这些值的示例如下：

    *   在“管理员用户名”字段中，填入 Zendesk 租户的管理员帐户的用户名。 示例：admin@contoso.com。

    *   在“机密令牌”字段中，填充步骤 6 中所述的机密令牌。

    *   在“域”字段中，填充 Zendesk 租户的子域。
    示例：对于租户 URL 为 https://my-tenant.zendesk.com 的帐户，子域为 **my-tenant**。

6. Zendesk 帐户的“机密令牌”位于“管理”>“API”>“设置”中。 

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 填入步骤 5 中所示的字段后，单击“测试连接”以确保 Azure AD 可以连接到 Zendesk。 如果连接失败，请确保 Zendesk 帐户具有管理员权限，然后重试。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 单击“ **保存**”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zendesk”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Zendesk 的用户属性。 选为“匹配”属性的特性用于匹配 Zendesk 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Zendesk”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Zendesk 的组属性。 选为“匹配”属性的特性用于匹配 Zendesk 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](./../active-directory-saas-scoping-filters.md)中提供的以下说明。

15. 若要为 Zendesk 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Zendesk 的用户和/或组。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 已准备好预配时，单击“保存”。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Zendesk 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="connector-limitations"></a>连接器限制
* Zendesk 仅支持对“代理”角色的用户使用组。 有关详细信息，请参阅 [Zendesk 的文档](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
