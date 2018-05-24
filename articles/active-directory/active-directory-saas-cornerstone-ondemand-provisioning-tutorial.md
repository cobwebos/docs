---
title: 教程：使用 Azure Active Directory 为 Cornerstone OnDemand 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Cornerstone OnDemand 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 9754058869d790f99e3c44db6ddd31b0ecb7a4e4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763401"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教程：为 Cornerstone OnDemand 配置自动用户预配


本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Cornerstone OnDemand 以及取消其预配需在 Cornerstone OnDemand 和 Azure Active Directory (Azure AD) 中执行的步骤。


> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](./active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

*   Azure AD 租户
*   Cornerstone OnDemand 租户
*   Cornerstone OnDemand 中具有管理员权限的用户帐户


> [!NOTE]
> Azure AD 预配集成依赖于可供 Cornerstone OnDemand 团队使用的 [Cornerstone OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>从库中添加 Cornerstone OnDemand
在使用 Azure AD 为 Cornerstone OnDemand 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Cornerstone OnDemand 添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库中添加 Cornerstone OnDemand，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]
    
3. 若要添加 Cornerstone OnDemand，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cornerstone OnDemand”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppSearch.png)

5. 在结果面板中，选择“Cornerstone OnDemand”，然后单击“添加”按钮将 Cornerstone OnDemand 添加到 SaaS 应用程序列表。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。 

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Cornerstone OnDemand。 确定后，可以按照此处的说明将这些用户和/或组分配到 Cornerstone OnDemand：

*   [向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand 的重要提示

*   建议将单个 Azure AD 用户分配到 Cornerstone OnDemand 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Cornerstone OnDemand，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>配置 Cornerstone OnDemand 的自动用户预配

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Cornerstone OnDemand 中创建、更新和禁用用户和/或组。


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>若要在 Azure AD 中为 Cornerstone OnDemand 配置自动用户预配，请执行以下操作：


1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Cornerstone OnDemand。
 
    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/Successcenter2.png)

3. 选择“预配”选项卡。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入 Cornerstone OnDemand 帐户的**管理员用户名**、**管理员密码**和**域**。

    *   在“管理员用户名”字段中，填入 Cornerstone OnDemand 租户中管理员帐户的域\用户名。 示例：contoso.com。

    *   在“管理员密码”字段中，填入管理员用户名所对应的密码。

    *   在“域”字段中，填入 Cornerstone OnDemand 租户的 Web 服务 URL。 示例：该服务位于 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，对于 Contoso，域为 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有关如何检索 Web 服务 URL 的详细信息，请参阅[此处](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填入步骤 5 中所示的字段后，请单击“测试连接”以确保 Azure AD 可以连接到 Cornerstone OnDemand。 如果连接失败，请确保 Cornerstone OnDemand 帐户具有管理员权限，然后重试。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Cornerstone OnDemand”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在“属性映射”部分中，查看从 Azure AD 同步到 Cornerstone OnDemand 的用户属性。 选为“匹配”属性的特性用于匹配 Cornerstone OnDemand 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要配置范围筛选器，请参阅[范围筛选器教程](./active-directory-saas-scoping-filters.md)中提供的以下说明。

12. 若要为 Cornerstone OnDemand 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Cornerstone OnDemand 的用户和/或组。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 已准备好预配时，单击“保存”。

    ![Cornerstone OnDemand 预配](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/Save.png)


此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Cornerstone OnDemand 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](./active-directory-saas-provisioning-reporting.md)。
## <a name="connector-limitations"></a>连接器限制

* Cornerstone OnDemand“位置”特性需要一个与 Cornerstone OnDemand 门户上的角色相对应的值。 可以通过导航到 Cornerstone OnDemand 门户中的“编辑用户记录”>“组织结构”>“位置”获取有效的“位置”值列表。
    ![Cornerstone OnDemand 预配编辑用户](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![Cornerstone OnDemand 预配位置](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![Cornerstone OnDemand 预配位置列表](./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/PostionId.png)
    
## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
