---
title: 教程：使用 Azure Active Directory 为 Cornerstone OnDemand 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Cornerstone OnDemand 以及取消其预配。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4200bc9879aba5e18282123be03576cc617a5e14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549222"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教程：为 Cornerstone OnDemand 配置自动用户预配

本教程演示要将 Azure AD 配置为自动将用户或组预配到 Cornerstone OnDemand 以及取消其预配，需在 Cornerstone OnDemand 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务基础上构建的连接器。 有关此服务的功能、工作原理以及常见问题的信息，请参阅[使用 Azure Active Directory 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你具有：

* Azure AD 租户。
* Cornerstone OnDemand 租户。
* Cornerstone OnDemand 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于 [Cornerstone OnDemand Web 服务](https://www.cornerstoneondemand.com/)。 此服务可供 Cornerstone OnDemand 团队使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>从 Azure 市场添加 Cornerstone OnDemand

在使用 Azure AD 为 Cornerstone OnDemand 配置自动用户预配之前，请从市场将 Cornerstone OnDemand 添加到托管 SaaS 应用程序列表。

若要从市场添加 Cornerstone OnDemand，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Azure Active Directory”。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 Cornerstone OnDemand，然后从结果面板中选择“Cornerstone OnDemand”。 若要添加该应用程序，请选择“添加”。

    ![结果列表中的“Cornerstone OnDemand”](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 Cornerstone OnDemand。 若要将这些用户或组分配到 Cornerstone OnDemand，请按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand 的重要提示

* 建议将单个 Azure AD 用户分配到 Cornerstone OnDemand，以测试自动用户预配配置。 可稍后再分配其他用户或组。

* 将用户分配到 Cornerstone OnDemand 时，请在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>配置 Cornerstone OnDemand 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它可以基于 Azure AD 中的用户或组分配在 Cornerstone OnDemand 中创建、更新和禁用用户或组。

若要在 Azure AD 中为 Cornerstone OnDemand 配置自动用户预配，请执行以下步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” > “Cornerstone OnDemand”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cornerstone OnDemand”。

    ![应用程序列表中的“Cornerstone OnDemand”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Cornerstone OnDemand 预配](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Cornerstone OnDemand 预配模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入 Cornerstone OnDemand 帐户的管理员用户名、管理员密码和域：

    * 在“管理员用户名”框中，填入 Cornerstone OnDemand 租户中管理员帐户的域或用户名。 例如 contoso\admin。

    * 在“管理员密码”框中，填入管理员用户名所对应的密码。

    * 在“域”框中，填入 Cornerstone OnDemand 租户的 Web 服务 URL。 例如，该服务位于 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，对于 Contoso，域为 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有关如何检索 Web 服务 URL 的详细信息，请参阅[此 PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填写步骤 5 中所示的框后，选择“测试连接”以确保 Azure AD 可以连接到 Cornerstone OnDemand。 如果连接失败，请确保 Cornerstone OnDemand 帐户具有管理员权限，然后重试。

    ![Cornerstone OnDemand 测试连接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在“通知电子邮件”框中，输入要接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![Cornerstone OnDemand 通知电子邮件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 选择“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Cornerstone OnDemand” 。

    ![Cornerstone OnDemand 同步](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在“属性映射”部分中，查看从 Azure AD 同步到 Cornerstone OnDemand 的用户属性。 选为“匹配”属性的特性用于匹配 Cornerstone OnDemand 中的用户帐户以执行更新操作。 若要保存任何更改，请选择“保存”。

    ![Cornerstone OnDemand 属性映射](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

12. 若要为 Cornerstone OnDemand 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![Cornerstone OnDemand 预配状态](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定义要预配到 Cornerstone OnDemand 的用户或组。 在“设置”部分的“范围”中，选择所需的值。

    ![Cornerstone OnDemand 范围](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 准备好预配时，选择“保存”。

    ![Cornerstone OnDemand 保存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此操作会对“设置”部分的“范围”中定义的所有用户或组启动初始同步 。 初始同步所需的时间比后续同步要长。 只要运行 Azure AD 预配服务，大约每 40 分钟就会发生一次同步。 

可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Cornerstone OnDemand 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

Cornerstone OnDemand“位置”特性需要一个与 Cornerstone OnDemand 门户上的角色相对应的值。 若要获取有效“位置”值的列表，请转到 Cornerstone OnDemand 门户中的“编辑用户记录”>“组织结构”>“位置”。

![Cornerstone OnDemand 预配编辑用户记录](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 预配位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 预配位置列表](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
