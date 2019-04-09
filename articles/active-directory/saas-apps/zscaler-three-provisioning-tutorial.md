---
title: 教程：配置自动用户预配使用 Azure Active Directory 的三个 Zscaler |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配到 Zscaler 三个用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: ed158ae825ec8aac24a57eb0f5a986b2124b66fb
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057789"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>教程：将 Zscaler 三个配置自动用户预配

本教程的目的是为了演示在 Zscaler 三个和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消其预配的用户和/或组到三个 Zscaler 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../active-directory-saas-app-provisioning.md)。
>
> 此连接器当前处于公共预览状态。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[补充使用条款的 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

本教程中所述的方案假定你已具备以下项：

* Azure AD 租户
* 三个 Zscaler 租户
* 在 Zscaler 三个具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler 三个 SCIM API，可供企业包使用的帐户的 Zscaler 三名开发人员。

## <a name="adding-zscaler-three-from-the-gallery"></a>从库添加 Zscaler Three

配置自动用户预配 Azure AD 与 Zscaler 三个时之前, 需要三个 Zscaler 从库中添加 Azure AD 应用程序到托管 SaaS 应用程序的列表。

**若要从 Azure AD 应用程序库添加 Zscaler 三个，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zscaler Three”，在结果面板中选择“Zscaler Three”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Zscaler Three](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-three"></a>将用户分配到 Zscaler 三个

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配前, 应确定哪些用户和/或 Azure AD 中的组需要访问 Zscaler 三个。 确定后，您可以将这些用户和/或组到 Zscaler 三个分配按照此处的说明：

* [向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>将用户分配到 Zscaler 三个重要提示

* 建议将单个 Azure AD 用户分配到 Zscaler 三个以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Zscaler 三个，必须在分配对话框中选择任何有效的特定于应用程序角色 （如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-zscaler-three"></a>配置自动用户预配到 Zscaler 三个

本部分将指导您完成配置 Azure AD 预配服务以创建、 更新和禁用用户和/或组在 Zscaler 三个用户和/或组分配基于在 Azure AD 中的步骤。

> [!TIP]
> 您还可以选择启用基于 SAML 的单一登录的 Zscaler 三个中的说明提供[Zscaler 三个单一登录教程](zscaler-three-tutorial.md)。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-three-in-azure-ad"></a>若要配置自动用户在 Azure AD 中预配为 Zscaler 三个：

1. 登录到[Azure 门户](https://portal.azure.com)，然后选择**企业应用程序**，选择**所有应用程序**，然后选择**Zscaler 三个**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler Three”。

    ![应用程序列表中的 Zscaler 三个链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”设置为“自动”。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. 下**管理员凭据**部分中，输入**租户 URL**并**机密令牌**Zscaler 三个帐户在步骤 6 中所述。

6. 若要获取**租户 URL**并**机密令牌**，导航到**管理 > 身份验证设置**在 Zscaler 三个门户用户界面上单击**SAML**下**身份验证类型**。 

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    单击**配置 SAML**以打开**配置 SAML**选项。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    选择**Enable SCIM-Based 预配**检索**基 URL**并**持有者令牌**，然后保存设置。 复制**基 URL**到**租户 URL**并**持有者令牌**到**机密令牌**在 Azure 门户中。

7. 填入步骤 5 中所示的字段后，单击**测试连接**以确保 Azure AD 可以连接到 Zscaler 三个。 如果连接失败，请确保你的 Zscaler 三个帐户具有管理员权限，然后重试。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/notification.png)

9. 单击“ **保存**”。

10. 下**映射**部分中，选择**Azure Active Directory 用户同步到 Zscaler 三个**。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. 查看从 Azure AD 同步到 Zscaler 三个中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配三个 Zscaler 中以执行更新操作的用户帐户。 选择“保存”按钮以提交任何更改。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. 下**映射**部分中，选择**Azure Active Directory 组同步到 Zscaler 三个**。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. 查看从 Azure AD 同步到 Zscaler 三个中的组属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配以执行更新操作中 Zscaler 三个组。 选择“保存”按钮以提交任何更改。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](./../active-directory-saas-scoping-filters.md)中提供的以下说明。

15. 若要启用 Azure AD 预配服务的三个 Zscaler，请更改**预配状态**到**上**中**设置**部分。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. 通过选择所需的值中定义预配到 Zscaler 三个用户和/或组你想**作用域**中**设置**部分。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. 已准备好预配时，单击“保存”。

    ![Zscaler 三个预配](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告，其中描述了由 Azure AD 预配服务对 Zscaler 三个执行所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
