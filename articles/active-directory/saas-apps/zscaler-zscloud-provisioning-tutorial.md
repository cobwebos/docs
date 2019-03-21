---
title: 教程：配置自动用户预配 Azure Active Directory 与 Zscaler ZSCloud |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配到 Zscaler ZSCloud 用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3f7fcd59bafe5619a1ef411bf81a6b8c3431f22c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087408"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>教程：将 Zscaler ZSCloud 配置自动用户预配

本教程的目的是为了演示在 Zscaler ZSCloud 和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消其预配的用户和/或组到 Zscaler ZSCloud 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../active-directory-saas-app-provisioning.md)。
> 
> 此连接器当前处于公共预览状态。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[补充使用条款的 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

本教程中所述的方案假定你已具备以下项：

*   Azure AD 租户
*   Zscaler ZSCloud 租户
*   Zscaler ZSCloud 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler ZSCloud SCIM API，可供企业包使用的帐户的 Zscaler ZSCloud 开发人员。

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>从库添加 Zscaler ZSCloud
配置自动用户预配 Azure AD 与 Zscaler ZSCloud 之前, 需要从 Azure AD 应用程序库将 Zscaler ZSCloud 添加到托管 SaaS 应用程序的列表。

**若要从 Azure AD 应用程序库添加 Zscaler ZSCloud，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]

3. 若要添加 Zscaler ZSCloud，请单击**新的应用程序**在对话框顶部的按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入 **Zscaler ZSCloud**。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/appsearch.png)

5. 在结果面板中，选择**Zscaler ZSCloud**，然后单击**添加**按钮将 Zscaler ZSCloud 添加到 SaaS 应用程序列表。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/appsearchresults.png)

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/appcreation.png)

## <a name="assigning-users-to-zscaler-zscloud"></a>将用户分配到 Zscaler ZSCloud

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配前, 应确定哪些用户和/或 Azure AD 中的组需要访问 Zscaler ZSCloud。 确定后，您可以将这些用户和/或组到 Zscaler ZSCloud 分配按照此处的说明：

*   [向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>将用户分配到 Zscaler ZSCloud 的重要提示

*   建议将单个 Azure AD 用户分配到 Zscaler ZSCloud，以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Zscaler ZSCloud，必须在分配对话框中选择任何有效的特定于应用程序角色 （如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-zscaler-zscloud"></a>配置自动用户预配到 Zscaler ZSCloud

本部分将指导你完成步骤以配置 Azure AD 预配服务以创建、 更新和禁用用户和/或组在 Zscaler ZSCloud 中基于用户和/或组分配在 Azure AD 中。

> [!TIP]
> 您还可以选择启用基于 SAML 的单一登录的 Zscaler ZSCloud 中的说明提供[Zscaler ZSCloud 单一登录教程](zscaler-zsCloud-tutorial.md)。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-zscloud-in-azure-ad"></a>若要配置自动用户在 Azure AD 中预配为 Zscaler ZSCloud:

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Zscaler ZSCloud。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/appinstancesearch.png)

3. 选择“预配”选项卡。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. 将“预配模式”设置为“自动”。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. 下**管理员凭据**部分中，输入**租户 URL**并**机密令牌**Zscaler ZSCloud 帐户在步骤 6 中所述。

6. 若要获取**租户 URL**并**机密令牌**，导航到**管理 > 身份验证设置**在 Zscaler ZSCloud 门户用户界面上单击**SAML**下**身份验证类型**。 

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    单击**配置 SAML**以打开**配置 SAML**选项。 

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)
    
    选择**Enable SCIM-Based 预配**检索**基 URL**并**持有者令牌**，然后保存设置。 复制**基 URL**到**租户 URL**并**持有者令牌**到**机密令牌**在 Azure 门户中。

7. 填入步骤 5 中所示的字段后，单击**测试连接**以确保 Azure AD 可以连接到 Zscaler ZSCloud。 如果连接失败，请确保你的 Zscaler ZSCloud 帐户具有管理员权限，然后重试。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)
    
8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. 单击“ **保存**”。

10. 下**映射**部分中，选择**Azure Active Directory 用户同步到 Zscaler ZSCloud**。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. 查看从 Azure AD 同步到 Zscaler ZSCloud 中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配 Zscaler ZSCloud 中以执行更新操作的用户帐户。 选择“保存”按钮以提交任何更改。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. 下**映射**部分中，选择**Azure Active Directory 组同步到 Zscaler ZSCloud**。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. 查看从 Azure AD 同步到 Zscaler ZSCloud 中的组属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配 Zscaler ZSCloud 中以执行更新操作的组。 选择“保存”按钮以提交任何更改。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](./../active-directory-saas-scoping-filters.md)中提供的以下说明。

15. 若要启用 Azure AD 预配服务为 Zscaler ZSCloud，请更改**预配状态**到**上**中**设置**部分。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. 选择所需的值中预配到 Zscaler ZSCloud 中定义的用户和/或组你想**作用域**中**设置**部分。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. 已准备好预配时，单击“保存”。

    ![预配的 Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告，其中描述了由 Azure AD 预配服务对 Zscaler ZSCloud 执行所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
