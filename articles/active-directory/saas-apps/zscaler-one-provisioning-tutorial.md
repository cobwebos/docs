---
title: 教程：为 Zscaler 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动预配和取消设置用户帐户 Zscaler。
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
ms.openlocfilehash: de9dce04b6f27b6ae6f5c5caeed5728370359558
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515389"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Zscaler

本教程演示在 Zscaler 中执行的步骤, 并 Azure Active Directory (Azure AD) 将 Azure AD 配置为自动预配和取消设置用户和组 Zscaler。

> [!NOTE]
> 本教程介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 有关此服务的用途、工作原理和常见问题的信息, 请参阅[使用 Azure Active Directory 自动执行用户预配和取消预配到软件即服务 (SaaS) 应用程序](../active-directory-saas-app-provisioning.md)。


## <a name="prerequisites"></a>系统必备

本教程中概述的方案假定你具有:

* Azure AD 租户。
* Zscaler 一个租户。
* Zscaler 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler One SCIM API。 此 API 可用于 Zscaler 一个开发人员使用企业包的帐户。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>从 Azure Marketplace 添加 Zscaler

在将 Zscaler 配置为 Azure AD 的自动用户预配之前, 请将 Azure Marketplace 中的 Zscaler 添加到托管的 SaaS 应用程序列表。

若要从 Marketplace 中添加 Zscaler, 请执行以下步骤。

1. 在[Azure 门户](https://portal.azure.com)的左侧导航窗格中, 选择 " **Azure Active Directory**"。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中, 输入 " **Zscaler** ", 然后从 "结果" 面板中选择 " **Zscaler** "。 若要添加应用程序, 请选择 "**添加**"。

    ![结果列表中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>将用户分配到 Zscaler

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中, 只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前, 决定 Azure AD 中的哪些用户或组需要访问 Zscaler。 若要将这些用户或组分配到 Zscaler, 请按照[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>将用户分配到 Zscaler 的重要提示

* 建议你将单个 Azure AD 用户分配到 Zscaler 一个用户来测试自动用户预配配置。 稍后可以分配其他用户或组。

* 将用户分配到 Zscaler 时, 在 "分配" 对话框中选择任何特定于应用程序的有效角色 (如果可用)。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>配置 Zscaler 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它基于 Azure AD 中的用户或组分配在 Zscaler 中创建、更新和禁用用户或组。

> [!TIP]
> 还可以为 Zscaler 启用基于 SAML 的单一登录。 按照[Zscaler 单一登录教程](zscaler-One-tutorial.md)中的说明进行操作。 可以独立于自动用户预配配置单一登录, 尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中为 Zscaler 配置自动用户预配

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序** > " "**所有应用程序** > **Zscaler**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler One”。

    ![应用程序列表中的 Zscaler 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Zscaler 一个预配](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”设置为“自动”。

    ![Zscaler 一种设置模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在 "**管理员凭据**" 部分下, 根据步骤6中所述, 在 "**租户 URL** " 和 "**机密令牌**" 框中填写 Zscaler 的设置。

6. 若要获取租户 URL 和机密令牌, 请在 Zscaler One portal UI 中转到 "**管理** > " "**身份验证设置**"。 在“身份验证类型”下选择“SAML”。

    ![Zscaler 一个身份验证设置](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 选择 "**配置 saml** " 打开 "**配置 saml**选项"。

    ![Zscaler One 配置 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 选择 "**启用基于 SCIM 的设置**", 以获取**基本 URL**和**持有者令牌**中的设置。 然后保存设置。 将**基 url**设置复制到 Azure 门户中的**租户 url** 。 将 "**持有者令牌**" 设置复制到 Azure 门户中的 "**机密令牌**"。

7. 填写步骤5中所示的框后, 请选择 "**测试连接**" 以确保 Azure AD 可以连接到 Zscaler。 如果连接失败, 请确保 Zscaler 一个帐户具有管理员权限, 然后重试。

    ![Zscaler 一个测试连接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在 "**通知电子邮件**" 框中, 输入要接收设置错误通知的个人或组的电子邮件地址。 选中 "**发生故障时发送电子邮件通知**" 复选框。

    ![Zscaler 一封通知电子邮件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 选择**保存**。

10. 在 "**映射**" 部分下, 选择 "**将 Azure Active Directory 用户同步到 Zscaler**"。

    ![Zscaler 一个用户同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到 Zscaler 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Zscaler 中的用户帐户以执行更新操作。 若要保存任何更改, 请选择 "**保存**"。

    ![Zscaler 一个匹配的用户属性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 在 "**映射**" 部分下, 选择 "**同步 Azure Active Directory 组" 以 Zscaler 一个组**。

    ![Zscaler 一个组同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到 Zscaler 的组属性。 选为 "**匹配**" 属性的特性用于匹配 Zscaler 中的组以执行更新操作。 若要保存任何更改, 请选择 "**保存**"。

    ![Zscaler 一个匹配的组属性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器, 请按照[范围筛选器教程](./../active-directory-saas-scoping-filters.md)中的说明进行操作。

15. 若要为 Zscaler 启用 Azure AD 预配服务, 请在 "**设置**" 部分中, 将 "**预配状态**" 更改为 **"打开**"。

    ![Zscaler 一个预配状态](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定义要预配到 Zscaler 的用户或组。 在 "**设置**" 部分的 "**范围**" 中选择所需的值。

    ![Zscaler 一个作用域](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 准备好进行预配时, 请选择 "**保存**"。

    ![Zscaler 一个保存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此操作将启动 "**设置**" 部分的 "**范围**" 中定义的所有用户或组的初始同步。 初始同步执行的时间比后续同步长。 只要运行 Azure AD 预配服务, 它们大约每40分钟发生一次。 

您可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接。 该报表介绍 Azure AD 预配服务对 Zscaler 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
