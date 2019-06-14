---
title: 教程：将 Zscaler One 配置自动用户预配使用 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消预配到 Zscaler One 用户帐户。
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
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706603"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教程：将 Zscaler One 配置自动用户预配

本教程演示了在 Zscaler One 和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消预配的用户和组添加到 Zscaler One 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用，它的工作原理，以及常见问题的信息，请参阅[自动用户预配和取消预配到软件作为-服务 (SaaS) 应用程序与 Azure Active Directory](../active-directory-saas-app-provisioning.md)。
>
> 此连接器目前以预览版的形式。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

在本教程中所述的方案假定你拥有：

* Azure AD 租户。
* Zscaler One 租户。
* Zscaler One 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler 一个 SCIM API。 此 API 可供企业包使用的帐户的 Zscaler One 开发人员。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>从 Azure Marketplace 中添加 Zscaler One

您配置自动用户预配 Azure AD 与 Zscaler One 之前，Zscaler One 通过 Azure Marketplace 添加到托管 SaaS 应用程序的列表。

若要从 Marketplace 添加 Zscaler One，请执行以下步骤。

1. 在中[Azure 门户](https://portal.azure.com)，在左侧导航窗格中，选择**Azure Active Directory**。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”  。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Zscaler One** ，然后选择**Zscaler One**结果面板中。 若要添加该应用程序，请选择**添加**。

    ![结果列表中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>将用户分配到 Zscaler One

Azure Active Directory 使用称为的概念*分配*来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步的用户或组分配给 Azure AD 中的应用程序。

配置和启用自动用户预配之前，确定哪些用户或 Azure AD 中的组需要访问 Zscaler One。 若要将这些用户或组分配到 Zscaler One，请按照中的说明[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>将用户分配到 Zscaler One 的重要提示

* 我们建议将分配一个 Azure AD 用户与 Zscaler One 测试自动用户预配配置。 可以稍后分配其他用户或组。

* 当将用户分配到 Zscaler One 时，请选择任何有效的特定于应用程序的角色，如果可用，在分配对话框中。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>配置自动用户预配到 Zscaler One

本部分将指导您完成配置 Azure AD 预配服务的步骤。 在 Azure AD 中，使用它来创建、 更新和禁用用户或组 Zscaler One 中的基于用户或组分配。

> [!TIP]
> 此外可以启用基于 SAML 的单一登录 Zscaler one。 按照中的说明[Zscaler One 单一登录教程](zscaler-One-tutorial.md)。 单一登录可以独立于配置自动用户预配，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>配置自动用户在 Azure AD 中预配 Zscaler one

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序** > **的所有应用程序** > **Zscaler One**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler One”  。

    ![应用程序列表中的 Zscaler One 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![Zscaler One 预配](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”  设置为“自动”  。

    ![Zscaler 一种预配模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 下**管理员凭据**部分中，填写**租户 URL**并**机密令牌**框的设置你的 Zscaler One 帐户在步骤 6 中所述。

6. 若要获取租户 URL 和机密令牌，请转到**Administration** > **身份验证设置**Zscaler One 的门户 UI 中。 在“身份验证类型”下选择“SAML”。  

    ![Zscaler One 身份验证设置](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 选择**配置 SAML**以打开**配置 SAML**选项。

    ![Zscaler One 配置 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 选择**Enable SCIM-Based 预配**获取设置**基 URL**并**持有者令牌**。 然后保存设置。 复制**基 URL**将设置为**租户 URL**在 Azure 门户中。 复制**持有者令牌**将设置为**机密令牌**在 Azure 门户中。

7. 步骤 5 中所示的框中填充后，选择**测试连接**以确保 Azure AD 可以连接到 Zscaler One。 如果连接失败，请确保你的 Zscaler One 帐户具有管理员权限，然后重试。

    ![Zscaler 一个测试连接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在中**通知电子邮件**框中，输入用户的电子邮件地址或组以接收预配错误通知。 选择**发生故障时发送电子邮件通知**复选框。

    ![Zscaler One 通知电子邮件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 选择“保存”。 

10. 下**映射**部分中，选择**Azure Active Directory 用户同步到 Zscaler One**。

    ![Zscaler One 用户同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 查看从 Azure AD 同步到 Zscaler One 中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配 Zscaler One 中以执行更新操作的用户帐户。 若要保存任何更改，请选择**保存**。

    ![Zscaler One 匹配的用户属性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 下**映射**部分中，选择**Azure Active Directory 组同步到 Zscaler One**。

    ![Zscaler One 的组同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 查看从 Azure AD 同步到 Zscaler One 中的组属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配 Zscaler One 中的组以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Zscaler One 匹配的组属性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器，按照中的说明[作用域筛选器教程](./../active-directory-saas-scoping-filters.md)。

15. 若要启用 Azure AD 中预配服务 Zscaler one**设置**部分中，更改**预配状态**到**上**。

    ![Zscaler 一个预配状态](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定义的用户或组所需预配到 Zscaler One。 在中**设置**部分中，选择在所需的值**作用域**。

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 准备好预配后，选择**保存**。

    ![一个保存 Zscaler](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此操作会启动初始同步所有用户或组中定义**作用域**中**设置**部分。 初始同步长，若要执行的时间比更高版本的同步。 只要 Azure AD 预配服务运行，它们会大约每隔 40 分钟。 

可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告。 报告描述了预配服务对 Zscaler One 的 Azure AD 执行的所有操作。

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
