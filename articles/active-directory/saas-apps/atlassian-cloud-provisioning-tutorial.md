---
title: 教程：为 Atlassian Cloud 配置 Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Atlassian Cloud 并取消其预配。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561583"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Atlassian 云

本教程的目的是演示要在 Atlassian 云和 Azure Active Directory (Azure AD) 中执行的步骤, 以将 Azure AD 自动预配和取消预配到 Atlassian 云。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。


## <a name="prerequisites"></a>系统必备

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Atlassian 云租户](https://www.atlassian.com/licensing/cloud)
* Atlassian Cloud 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于可供 Atlassian 云团队使用的**Atlassian CLOUD SCIM API**。

## <a name="add-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud

在为 Atlassian Cloud 配置 Azure AD 的自动用户预配之前, 需要将 Azure AD 应用程序库中的 Atlassian Cloud 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加 Atlassian Cloud, 请执行以下步骤:**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中, 选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序, 请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中, 输入 " **Atlassian cloud**", 在结果面板中选择 " **Atlassian cloud** ", 并单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Atlassian Cloud](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>将用户分配到 Atlassian Cloud

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中, 只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前, 你应该确定 Azure AD 中的哪些用户和/或组需要访问 Atlassian Cloud。 确定后, 可按照此处的说明将这些用户和/或组分配到 Atlassian Cloud:

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>将用户分配到 Atlassian Cloud 的重要提示

* 建议将单个 Azure AD 用户分配到 Atlassian Cloud 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Atlassian Cloud 时, 必须在分配对话框中选择任何特定于应用程序的有效角色 (如果可用)。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>配置 Atlassian Cloud 的自动用户预配 

本部分将指导你完成以下步骤: 配置 Azure AD 预配服务, 以便基于 Azure AD 中的用户和/或组分配在 Atlassian Cloud 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照[Atlassian cloud 单一登录教程](atlassian-cloud-tutorial.md)中提供的说明为 Atlassian Cloud 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>若要在 Azure AD 中配置 Atlassian Cloud 的自动用户预配:

1. 登录到[Azure 门户](https://portal.azure.com), 选择 "**企业应用程序**", 选择 "**所有应用程序**", 然后选择 " **Atlassian Cloud**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Atlassian Cloud”。

    ![应用程序列表中的 Atlassian Cloud 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”设置为“自动”。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. 在 "**管理员凭据**" 部分中, 输入 Atlassian 云帐户的**租户 URL**和**机密令牌**。 这些值的示例如下：

   * 在 "**租户 URL** " 字段中, 根据步骤6中所述, 填写从 Atlassian 接收的特定租户终结点。 例如: `https://api.atlassian.com/scim/directory/{directoryId}`。

   * 在“机密令牌”字段中，填充步骤 6 中所述的机密令牌。

6. 导航到[Atlassian 组织管理器](https://admin.atlassian.com) **> 用户预配**, 并单击 "**创建令牌**"。 分别将**目录基 url**和**持有者令牌**复制到 "**租户 URL** " 和 "**机密令牌**" 字段。

    ![Atlassian 云预](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png)配![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 填充步骤5中所示的字段后, 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Atlassian Cloud。 如果连接失败, 请确保 Atlassian 云帐户具有管理员权限, 然后重试。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. 单击“保存”。

10. 在 "**映射**" 部分下, 选择 "**将 Azure Active Directory 用户同步到 Atlassian Cloud**"。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到 Atlassian Cloud 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 Atlassian Cloud 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. 在 "**映射**" 部分下, 选择 "**将 Azure Active Directory 组同步到 Atlassian Cloud**"。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到 Atlassian Cloud 的组属性。 选为 "**匹配**" 属性的属性用于匹配 Atlassian Cloud 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 若要为 Atlassian Cloud 启用 Azure AD 预配服务, 请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值, 定义要预配到 Atlassian Cloud 的用户和/或组。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. 已准备好预配时，单击“保存”。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接, 该报告描述了 Atlassian Cloud 上 Azure AD 预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Atlassian Cloud 仅允许从[已验证的域](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)预配用户。
* Atlassian Cloud 目前不支持组重命名。 这意味着, 对 Azure AD 中组的 displayName 的任何更改都不会在 Atlassian Cloud 中更新和反映。
* 仅当用户具有 Microsoft Exchange 邮箱时, 才填充 Azure AD 中**邮件**用户属性的值。 如果用户没有用户, 则建议将不同的所需属性映射到 Atlassian Cloud 中的**电子邮件**属性。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
