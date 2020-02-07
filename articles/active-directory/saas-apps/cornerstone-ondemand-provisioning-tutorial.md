---
title: 教程：使用 Azure Active Directory 为 Cornerstone OnDemand 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动预配用户帐户并将其取消预配到用户帐户。
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058394"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教程：为 Cornerstone OnDemand 配置自动用户预配

本教程演示了在 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到用户或组。

> [!NOTE]
> 本教程介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 有关此服务的用途、工作原理和常见问题的信息，请参阅[使用 Azure Active Directory 自动执行用户预配和取消预配到软件即服务（SaaS）应用程序](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你具有：

* Azure AD 租户。
* 基础 OnDemand 租户。
* 采用管理员权限的以基础为 OnDemand 的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[基础 OnDemand web 服务](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服务可供基础 OnDemand 团队使用。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>从 Azure Marketplace 添加基础 OnDemand

在配置 Azure AD 的自动用户预配的基础 OnDemand 之前，请将 Marketplace OnDemand 从 Marketplace 添加到托管的 SaaS 应用程序列表。

若要从 Marketplace 添加基础 OnDemand，请执行以下步骤。

1. 在[Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择 " **Azure Active Directory**"。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中 **，输入 "** "，然后从 "结果" 面板中选择 "**基础 ondemand** "。 若要添加应用程序，请选择 "**添加**"。

    ![结果列表中的“Cornerstone OnDemand”](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>将用户分配到基础 OnDemand

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 "基础" OnDemand。 若要将这些用户或组分配给用户或组，请按照向[企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand 的重要提示

* 建议将单个 Azure AD 用户分配到 "基础" OnDemand 以测试自动用户预配配置。 稍后可以分配其他用户或组。

* 将用户分配到 "基础" OnDemand 时，在 "分配" 对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>将自动用户预配配置为 "基础" OnDemand

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它基于 Azure AD 中的用户或组分配，在基础上创建、更新和禁用用户或组。

若要在 Azure AD 中配置适用于基础 OnDemand 的自动用户预配，请执行以下步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**" > "**所有应用程序** **" > "**

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cornerstone OnDemand”。

    ![应用程序列表中的 "基础 OnDemand" 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Cornerstone OnDemand 预配](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![基础 OnDemand 预配模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 在 "**管理员凭据**" 部分下，输入用户的管理员用户名、管理员密码和域。

    * 在 "**管理员用户名**" 框中，填写你的用户的用户帐户的域或用户名。 例如 contoso\admin。

    * 在 "**管理员密码**" 框中，填写与管理员用户名对应的密码。

    * 在 "**域**" 框中，填写 ""。 例如，该服务位于 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，而 Contoso 该域则 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有关如何检索 web 服务 URL 的详细信息，请参阅[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 填写步骤5中所示的框后，请选择 "**测试连接**" 以确保 Azure AD 可以连接到 "基础" OnDemand。 如果连接失败，请确保你的基础 OnDemand 帐户具有管理员权限，然后重试。

    ![基础 OnDemand 测试连接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在 "**通知电子邮件**" 框中，输入要接收设置错误通知的个人或组的电子邮件地址。 选中 "**发生故障时发送电子邮件通知**" 复选框。

    ![基础 OnDemand 通知电子邮件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 选择“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Cornerstone OnDemand”。

    ![基础 OnDemand 同步](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 "基础" OnDemand 的用户属性。 选为“匹配”属性的特性用于匹配 Cornerstone OnDemand 中的用户帐户以执行更新操作。 若要保存任何更改，请选择 "**保存**"。

    ![基础 OnDemand 属性映射](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

12. 若要为适用于基础的 OnDemand 启用 Azure AD 预配服务，请在 "**设置**" 部分中，将 "**预配状态**" 更改为 **"**

    ![基础 OnDemand 预配状态](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定义要预配到 "基础" OnDemand 的用户或组。 在 "**设置**" 部分的 "**范围**" 中选择所需的值。

    ![基础 OnDemand 范围](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 准备好进行预配时，请选择 "**保存**"。

    ![基础 OnDemand 保存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此操作将启动 "**设置**" 部分的 "**范围**" 中定义的所有用户或组的初始同步。 初始同步执行的时间比后续同步长。 只要运行 Azure AD 预配服务，它们大约每40分钟发生一次。 

您可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在基础 OnDemand 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

Cornerstone OnDemand“位置”特性需要一个与 Cornerstone OnDemand 门户上的角色相对应的值。 若要获取有效**位置**值的列表，请转到 "**编辑用户记录" > 组织结构中的 "编辑用户记录" > 位置**。

![基础 OnDemand 预配编辑用户记录](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![基础 OnDemand 预配位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![基础 OnDemand 预配位置列表](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
