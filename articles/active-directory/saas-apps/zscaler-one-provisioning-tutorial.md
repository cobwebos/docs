---
title: 教程：配置 Zscaler One，以便使用 Azure 活动目录进行自动用户预配 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配用户帐户并将其预配到 Zscaler One。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064166"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教程：配置 Zscaler One 进行自动用户预配

本教程演示了在 Zscaler One 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配用户和组到 Zscaler One。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用、工作方式以及常见问题的信息，请参阅[使用 Azure 活动目录 自动预配和取消预配软件即服务 （SaaS） 应用程序](../active-directory-saas-app-provisioning.md)。


## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定您具有：

* Azure AD 租户。
* Zscaler One 租户。
* Zscaler One 中的具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler One SCIM API。 此 API 可供 Zscaler One 开发人员使用企业包的帐户。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>从 Azure 应用商店添加 Zscaler One

在配置 Zscaler One 以使用 Azure AD 进行自动用户预配之前，请将 Zscaler One 从 Azure 应用商店添加到托管 SaaS 应用程序列表中。

要从应用商店中添加 Zscaler One，请按照以下步骤操作。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧的导航窗格中，选择**Azure 活动目录**。

    ![Azure 活动目录图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”****。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Zscaler One**并从结果面板中选择**Zscaler One。** 要添加应用程序，请选择"**添加**"。

    ![结果列表中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>将用户分配给 Zscaler One

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问 Zscaler One。 要将这些用户或组分配给 Zscaler One，请按照将[用户或组分配给企业应用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>将用户分配给 Zscaler One 的重要提示

* 我们建议您将单个 Azure AD 用户分配给 Zscaler One 以测试自动用户预配配置。 您可以稍后分配其他用户或组。

* 将用户分配给 Zscaler One 时，请在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>将自动用户预配配置为 Zscaler One

本节将指导您完成配置 Azure AD 预配服务的步骤。 使用它基于 Azure AD 中的用户或组分配在 Zscaler One 中创建、更新和禁用用户或组。

> [!TIP]
> 您还可以为 Zscaler One 启用基于 SAML 的单登录。 按照[Zscaler 单一登录教程](zscaler-One-tutorial.md)中的说明进行操作。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中为 Zscaler One 配置自动用户预配

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序** > **所有应用程序** > **Zscaler One**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler One”****。

    ![应用程序列表中的 Zscaler 一个链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![Zscaler 一个预配](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 将**预配模式**设置为 **"自动**"。

    ![Zscaler 一种预配模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在 **"管理员凭据"** 部分下，填写 **"租户 URL"** 和 **"秘密令牌"** 框，并填写步骤 6 中所述的 Zscaler One 帐户的设置。

6. 要获取租户 URL 和密钥令牌，请访问 Zscaler One 门户 UI 中的**管理** > **身份验证设置**。 在“身份验证类型”下选择“SAML”。********

    ![Zscaler 一个身份验证设置](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择 **"配置 SAML"** 以打开 **"配置 SAML"** 选项。

    ![Zscaler 一 配置 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b.保留“数据库类型”设置，即设置为“共享”。 选择**启用基于 SCIM 的预配**，以获取**基本 URL**和**承载令牌**中的设置。 然后保存设置。 将**基本 URL**设置复制到 Azure 门户中的**租户 URL。** 将**承载令牌**设置复制到 Azure 门户中**的秘密令牌**。

7. 填写步骤 5 中所示的框后，选择 **"测试连接**"以确保 Azure AD 可以连接到 Zscaler One。 如果连接失败，请确保您的 Zscaler One 帐户具有管理员权限，然后重试。

    ![Zscaler 一个测试连接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 **"通知电子邮件"** 框中，输入接收预配错误通知的个人或组的电子邮件地址。 选中"**在发生故障时发送电子邮件通知**"复选框。

    ![Zscaler 一封通知电子邮件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 选择“保存”。****

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 Zscaler One**。

    ![Zscaler 一个用户同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 在**属性映射**部分中查看从 Azure AD 同步到 Zscaler One 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Zscaler One 中的用户帐户以进行更新操作。 要保存任何更改，请选择"**保存**"。

    ![Zscaler 一个匹配的用户属性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 在 **"映射"** 部分下，选择**将 Azure 活动目录组同步到 Zscaler One**。

    ![Zscaler 一个组同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在**属性映射**部分中查看从 Azure AD 同步到 Zscaler One 的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Zscaler One 中的组以进行更新操作。 要保存任何更改，请选择"**保存**"。

    ![Zscaler 一个匹配组属性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 要配置范围筛选器，请按照[范围界定筛选器教程](./../active-directory-saas-scoping-filters.md)中的说明进行操作。

15. 要为 Zscaler One 启用 Azure AD 预配服务，在 **"设置"** 部分中，将**预配状态**更改为**On**。

    ![Zscaler 一种预配状态](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定义要预配到 Zscaler One 的用户或组。 在 **"设置"** 部分中，在 **"范围**"中选择所需的值。

    ![兹卡尔默一个范围](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 准备好预配时，请选择"**保存**"。

    ![兹卡尔瓦勒一保存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户或组的初始同步。 初始同步执行的时间比以后同步长。 只要 Azure AD 预配服务运行，它们大约每 40 分钟发生一次。 

您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Zscaler One 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
