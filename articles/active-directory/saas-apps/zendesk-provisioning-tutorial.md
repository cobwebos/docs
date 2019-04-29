---
title: 教程：使用 Azure Active Directory 为 Zendesk 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消预配到 Zendesk 的用户帐户。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114659"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教程：为 Zendesk 配置自动用户预配

本教程演示了在 Zendesk 和 Azure Active Directory (Azure AD) 配置 Azure AD 自动预配和取消预配用户和组到 Zendesk 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用，它的工作原理，以及常见问题的信息，请参阅[自动用户预配和取消预配到软件作为-服务 (SaaS) 应用程序与 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必备组件

在本教程中所述的方案假定你拥有：

* Azure AD 租户。
* 使用的 Zendesk 租户[企业](https://www.zendesk.com/product/pricing/)计划或更好地启用。
* Zendesk 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction)。 此 API 可供企业计划或更佳计划的 Zendesk 团队。

## <a name="add-zendesk-from-the-azure-marketplace"></a>从 Azure Marketplace 中添加 Zendesk

Zendesk 配置自动用户预配与 Azure AD 之前，将从 Azure Marketplace 添加 Zendesk 到托管 SaaS 应用程序的列表。

若要从 Marketplace 中添加 Zendesk，请执行以下步骤。

1. 在中[Azure 门户](https://portal.azure.com)，在左侧导航窗格中，选择**Azure Active Directory**。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Zendesk** ，然后选择**Zendesk**结果面板中。 若要添加该应用程序，请选择**添加**。

    ![结果列表中的 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>将用户分配到 Zendesk

Azure Active Directory 使用称为的概念*分配*来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步的用户或组分配给 Azure AD 中的应用程序。

配置和启用自动用户预配之前，确定哪些用户或 Azure AD 中的组需要访问 Zendesk。 若要将这些用户或组分配到 Zendesk，请按照中的说明[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>将用户分配到 Zendesk 的重要提示

* 今天，Zendesk 角色自动进行动态填充 Azure 门户 UI 中。 将 Zendesk 角色分配给用户之前，请确保对 Zendesk 检索 Zendesk 租户中的最新角色，完成初始同步。

* 我们建议将分配一个 Azure AD 用户到 Zendesk 来测试初始进行自动用户预配配置。 您可以分配其他用户或组更高版本后的测试都成功。

* 当将用户分配到 Zendesk 时，请选择任何有效的特定于应用程序的角色，如果可用，在分配对话框中。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>配置自动用户预配到 Zendesk 

本部分将指导您完成配置 Azure AD 预配服务的步骤。 使用它来创建、 更新和禁用用户或组在 Zendesk 中根据 Azure AD 中的用户或组分配。

> [!TIP]
> 此外可以启用基于 SAML 的单一登录为 Zendesk。 按照中的说明[Zendesk 单一登录教程](zendesk-tutorial.md)。 单一登录可以独立于配置自动用户预配，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>配置自动用户在 Azure AD 中预配为 Zendesk

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序** > **的所有应用程序** > **Zendesk**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zendesk”。

    ![应用程序列表中的 Zendesk 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 将“预配模式”设置为“自动”。

    ![Zendesk 预配模式](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 下**管理员凭据**部分中，输入管理员用户名、 机密令牌和 Zendesk 帐户的域。 这些值的示例如下：

   * 在中**管理员用户名**框中，填写你的 Zendesk 租户的管理员帐户的用户名。 例如 admin@contoso.com。

   * 在中**机密令牌**框中，在步骤 6 中所述填写的机密令牌。

   * 在中**域**框中，填充 Zendesk 租户的子域。 例如，对于具有的租户 URL 的帐户`https://my-tenant.zendesk.com`，你的子域是**我租户**。

6. 你的 Zendesk 帐户的机密令牌位于**管理员** > **API** > **设置**。 请确保**令牌访问**设置为**已启用**。

    ![Zendesk 管理员设置](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 机密令牌](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 步骤 5 中所示的框中填充后，选择**测试连接**，请确保 Azure AD 可以连接到 Zendesk。 如果连接失败，请确保 Zendesk 帐户具有管理员权限，然后重试。

    ![Zendesk 测试连接](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 在中**通知电子邮件**框中，输入用户的电子邮件地址或组以接收预配错误通知。 选择**发生故障时发送电子邮件通知**复选框。

    ![Zendesk 通知电子邮件](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zendesk”。

    ![Zendesk 用户同步](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 查看从 Azure AD 同步到 Zendesk 的用户属性**属性映射**部分。 选为“匹配”属性的特性用于匹配 Zendesk 中的用户帐户以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Zendesk 匹配用户属性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 下**映射**部分中，选择**Azure Active Directory 组同步到 Zendesk**。

    ![Zendesk 组同步](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 查看从 Azure AD 同步到 Zendesk 的组属性**属性映射**部分。 选为“匹配”属性的特性用于匹配 Zendesk 中的组以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Zendesk 匹配组属性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 若要配置范围筛选器，按照中的说明[作用域筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

15. 若要启用 Azure AD 中预配服务为 Zendesk**设置**部分中，更改**预配状态**到**上**。

    ![Zendesk 预配状态](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 定义的用户或组所需预配到 Zendesk。 在中**设置**部分中，选择在所需的值**作用域**。

    ![Zendesk 作用域](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 准备好预配后，选择**保存**。

    ![Zendesk 保存](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

此操作会启动初始同步所有用户或组中定义**作用域**中**设置**部分。 初始同步长，若要执行的时间比更高版本的同步。 只要 Azure AD 预配服务运行，它们会大约每隔 40 分钟。 

可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告。 报告描述了 Azure AD 预配服务对 Zendesk 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Zendesk 的用户与支持使用组**代理**仅角色。 有关详细信息，请参阅[Zendesk 文档](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

* 如果自定义角色分配给用户或组，Azure AD 自动用户预配服务，也将默认角色分配**代理**。 只有代理可以分配自定义角色。 有关详细信息，请参阅[Zendesk API 文档](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
