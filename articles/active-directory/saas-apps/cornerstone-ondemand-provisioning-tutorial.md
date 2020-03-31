---
title: 教程：使用 Azure Active Directory 为 Cornerstone OnDemand 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure 活动目录配置为自动预配用户帐户并将其预配到 Cornerstone OnDemand。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058394"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教程：为 Cornerstone OnDemand 配置自动用户预配

本教程演示了在基石按需和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配用户或组到基石按需。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用、工作方式以及常见问题的信息，请参阅[使用 Azure 活动目录 自动预配和取消预配软件即服务 （SaaS） 应用程序](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定您具有：

* Azure AD 租户。
* 基石按需租户。
* 具有管理员权限的 Cornerstone Demand 中的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于基石[OnDemand Web 服务](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服务可供基石点播团队使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>从 Azure 应用商店添加基石按需

在配置使用 Azure AD 自动用户预配的基石按需之前，请将应用商店中的基石按需添加到托管 SaaS 应用程序列表中。

要从应用商店添加基石按需，请按照以下步骤操作。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧的导航窗格中，选择**Azure 活动目录**。

    ![Azure 活动目录图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”****。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 **"需求基石"** 并从结果面板中选择 **"基石按需**"。 要添加应用程序，请选择"**添加**"。

    ![结果列表中的“Cornerstone OnDemand”](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>将用户分配到基石按需

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问 Cornerstone OnDemand。 要将这些用户或组分配给 Cornerstone OnDemand，请按照将[用户或组分配给企业应用](../manage-apps/assign-user-or-group-access-portal.md)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand 的重要提示

* 我们建议您将单个 Azure AD 用户分配给 Cornerstone OnDemand 以测试自动用户预配配置。 您可以稍后分配其他用户或组。

* 将用户分配给 Cornerstone OnDemand 时，请在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>将自动用户预配配置为基石按需

本节将指导您完成配置 Azure AD 预配服务的步骤。 使用它根据 Azure AD 中的用户或组分配在 Cornerstone OnDemand 中创建、更新和禁用用户或组。

要在 Azure AD 中配置基础按需的自动用户预配，请按照以下步骤操作。

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序** > **所有应用程序** > **基石按需**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cornerstone OnDemand”****。

    ![应用程序列表中的基石按需链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![Cornerstone OnDemand 预配](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”**** 设置为“自动”****。

    ![基石按需预配模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 **"管理员凭据"** 部分下，输入 Cornerstone OnDemand 帐户的管理员用户名、管理员密码和域：

    * 在 **"管理员用户名"** 框中，填写 Cornerstone OnDemand 租户上的管理员帐户的域或用户名。 例如 contoso_admin。

    * 在 **"管理员密码"** 框中，填写与管理员用户名对应的密码。

    * 在 **"域"** 框中，填写基石按需租户的 Web 服务 URL。 例如，服务位于`https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，对于 Contoso，域为`https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有关如何检索 Web 服务 URL 的详细信息，请参阅[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填写步骤 5 中所示的框后，选择 **"测试连接**"以确保 Azure AD 可以连接到基石按需。 如果连接失败，请确保您的 Cornerstone Demand 帐户具有管理员权限，然后重试。

    ![基石按需测试连接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 **"通知电子邮件"** 框中，输入接收预配错误通知的个人或组的电子邮件地址。 选中"**在发生故障时发送电子邮件通知**"复选框。

    ![基石按需通知电子邮件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 选择“保存”。****

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Cornerstone OnDemand”********。

    ![基石按需同步](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在**属性映射**部分中查看从 Azure AD 同步到基石按需的用户属性。 选为“匹配”属性的特性用于匹配 Cornerstone OnDemand 中的用户帐户以执行更新操作****。 要保存任何更改，请选择"**保存**"。

    ![基石按需属性映射](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 要配置范围筛选器，请按照[范围界定筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

12. 要启用"基础按需"的 Azure AD 预配服务，在 **"设置"** 部分中，将**预配状态**更改为**On**。

    ![基石按需预配状态](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定义要预配到基石按需的用户或组。 在 **"设置"** 部分中，在 **"范围**"中选择所需的值。

    ![基石按需范围](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 准备好预配时，请选择"**保存**"。

    ![基石按需保存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户或组的初始同步。 初始同步执行的时间比以后同步长。 只要 Azure AD 预配服务运行，它们大约每 40 分钟发生一次。 

您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Cornerstone OnDemand 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

Cornerstone OnDemand****“位置”特性需要一个与 Cornerstone OnDemand 门户上的角色相对应的值。 要获取有效**位置**值的列表，请转到"在基石按需门户中**编辑用户记录>组织结构>位置**。

![基石按需预配 编辑用户记录](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![基石按需配置位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![基石按需配置位置列表](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
