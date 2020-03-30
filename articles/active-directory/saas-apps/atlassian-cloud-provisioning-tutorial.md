---
title: 教程：使用 Azure 活动目录配置 Atlassian 云以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消预配到 Atlassian 云的用户帐户。
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
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059328"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>教程：为自动用户预配配置阿特拉斯云

本教程的目的是演示在 Atlassian 云和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 Atlassian 云预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [阿特拉斯云租户](https://www.atlassian.com/licensing/cloud)
* 具有管理员权限的 Atlassian 云中的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于**阿特拉斯云 SCIM API，** 该 API 可供阿特拉斯云团队使用。

## <a name="add-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud

在配置 Atlassian 云以使用 Azure AD 进行自动用户预配之前，需要将 Azure AD 应用程序库中的 Atlassian 云添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 Atlassian 云，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Atlassian 云**，在结果面板中选择**Atlassian 云**，然后单击"**添加**"按钮以添加应用程序。

    ![结果列表中的 Atlassian Cloud](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>将用户分配给阿特拉斯云

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 Atlassian 云。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 Atlassian 云：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>将用户分配给阿特拉斯云的重要提示

* 建议将单个 Azure AD 用户分配给 Atlassian Cloud 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Atlassian Cloud 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>配置自动用户预配到阿特拉斯云 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 Atlassian 云中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以根据[阿特拉斯云单一登录教程](atlassian-cloud-tutorial.md)中提供的说明，选择启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>要在 Azure AD 中配置 Atlassian 云的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)并选择**企业应用程序**，选择**所有应用程序**，然后选择**Atlassian 云**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Atlassian Cloud”。****

    ![应用程序列表中的 Atlassian Cloud 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. 将**预配模式**设置为 **"自动**"。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. 导航到[阿特拉斯组织管理器](https://admin.atlassian.com)**>选择组织>目录**。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. 单击 **"用户预配"，** 然后单击"**创建目录**"。 将**目录基本 URL**和**承载令牌**分别复制到**租户 URL**和**秘密令牌**字段。

    ![阿特拉斯](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png)![云配置阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)![阿特拉斯云配置](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 在 **"管理员凭据"** 部分下，输入 Atlassian 云帐户的**租户 URL**和**秘密令牌**。 这些值的示例如下：

   * 在 **"租户 URL"** 字段中，填写从 Atlassian 接收的特定租户终结点，如步骤 6 中所述。 例如： `https://api.atlassian.com/scim/directory/{directoryId}`.

   * 在 **"机密令牌"** 字段中，填充步骤 6 中所述的秘密令牌。

8. 填充步骤 7 中所示的字段后，单击 **"测试连接**"以确保 Azure AD 可以连接到 Atlassian 云。 如果连接失败，请确保您的 Atlassian 云帐户具有管理员权限，然后重试。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. 单击“保存”。****

11. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到阿特拉斯云**。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. 在**属性映射**部分中查看从 Azure AD 同步到 Atlassian 云的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Atlassian 云中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到阿特拉斯云**。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. 在**属性映射**部分中查看从 Azure AD 同步到 Atlassian 云的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Atlassian 云中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

16. 要为 Atlassian 云启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Atlassian 云的用户和/或组。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. 已准备好预配时，单击“保存”****。

    ![阿特拉斯云调配](./media/atlassian-cloud-provisioning-tutorial/save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 Atlassian 云上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* 阿特拉斯云只允许从[已验证的域](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)预配用户。
* 阿特拉斯云今天不支持组重命名。 这意味着对 Azure AD 中组显示名称的任何更改将不会更新并反映在 Atlassian 云中。
* 仅当用户具有 Microsoft Exchange 邮箱时，才会填充 Azure AD 中**的邮件**用户属性的值。 如果用户没有，建议将不同的所需属性映射到 Atlassian Cloud 中**的电子邮件**属性。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png