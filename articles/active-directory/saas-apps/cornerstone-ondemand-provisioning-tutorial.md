---
title: 教程：使用 Azure Active Directory 为 Cornerstone OnDemand 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消预配到 Cornerstone OnDemand 的用户帐户。
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
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127491"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>教程：为 Cornerstone OnDemand 配置自动用户预配

本教程演示了在 Cornerstone OnDemand 和 Azure Active Directory (Azure AD) 配置 Azure AD 自动预配和取消预配的用户或组到 Cornerstone OnDemand 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用，它的工作原理，以及常见问题的信息，请参阅[自动用户预配和取消预配到软件作为-服务 (SaaS) 应用程序与 Azure Active Directory](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必备组件

在本教程中所述的方案假定你拥有：

* Azure AD 租户。
* Cornerstone OnDemand 租户。
* Cornerstone OnDemand 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Cornerstone OnDemand web 服务](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)。 此服务可供 Cornerstone OnDemand 团队。

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>从 Azure Marketplace 中添加 Cornerstone OnDemand

为 Cornerstone OnDemand 配置自动用户预配与 Azure AD 之前，将从 Marketplace 添加 Cornerstone OnDemand 到托管 SaaS 应用程序的列表。

若要从 Marketplace 中添加 Cornerstone OnDemand，请执行以下步骤。

1. 在中[Azure 门户](https://portal.azure.com)，在左侧导航窗格中，选择**Azure Active Directory**。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Cornerstone OnDemand** ，然后选择**Cornerstone OnDemand**结果面板中。 若要添加该应用程序，请选择**添加**。

    ![结果列表中的“Cornerstone OnDemand”](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand

Azure Active Directory 使用称为的概念*分配*来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步的用户或组分配给 Azure AD 中的应用程序。

配置和启用自动用户预配之前，确定哪些用户或 Azure AD 中的组需要访问 Cornerstone OnDemand。 若要将这些用户或组分配到 Cornerstone OnDemand，请按照中的说明[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>将用户分配到 Cornerstone OnDemand 的重要提示

* 我们建议将分配一个 Azure AD 用户与 Cornerstone OnDemand 以测试自动用户预配配置。 可以稍后分配其他用户或组。

* 当将用户分配到 Cornerstone OnDemand 时，请选择任何有效的特定于应用程序的角色，如果可用，在分配对话框中。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>配置自动用户预配到 Cornerstone OnDemand

本部分将指导您完成配置 Azure AD 预配服务的步骤。 使用它来创建、 更新和禁用用户或组在 Azure AD 中基于用户或组分配在 Cornerstone OnDemand 中。

若要配置自动用户在 Azure AD 中预配为 Cornerstone OnDemand，请按照下列步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序** > **的所有应用程序** > **Cornerstone OnDemand**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cornerstone OnDemand”。

    ![应用程序列表中的 Cornerstone OnDemand 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![Cornerstone OnDemand 预配](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Cornerstone OnDemand 预配模式](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 下**管理员凭据**部分中，输入管理员用户名、 管理员密码和 Cornerstone OnDemand 帐户的域：

    * 在中**管理员用户名**框中，填写域或在 Cornerstone OnDemand 租户的管理员帐户的用户名。 例如，contoso\admin。

    * 在中**管理员密码**框中，填入管理员用户名所对应的密码。

    * 在中**域**框中，填入 Cornerstone OnDemand 租户的 web 服务 URL。 例如，该服务位于`https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`，为 Contoso，域为`https://ws-contoso.csod.com/feed30/clientdataservice.asmx`。 有关如何检索 web 服务 URL 的详细信息，请参阅[此 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)。

6. 步骤 5 中所示的框中填充后，选择**测试连接**，请确保 Azure AD 可以连接到 Cornerstone OnDemand。 如果连接失败，请确保你的 Cornerstone OnDemand 帐户具有管理员权限，然后重试。

    ![Cornerstone OnDemand 测试连接](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 在中**通知电子邮件**框中，输入用户的电子邮件地址或组以接收预配错误通知。 选择**发生故障时发送电子邮件通知**复选框。

    ![Cornerstone OnDemand 通知电子邮件](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. 选择“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Cornerstone OnDemand”。

    ![Cornerstone OnDemand 同步](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. 查看从 Azure AD 同步到 Cornerstone OnDemand 中的用户属性**属性映射**部分。 选为“匹配”属性的特性用于匹配 Cornerstone OnDemand 中的用户帐户以执行更新操作。 若要保存任何更改，请选择**保存**。

    ![Cornerstone OnDemand 属性映射](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 若要配置范围筛选器，按照中的说明[作用域筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

12. 若要启用 Azure AD 中预配服务为 Cornerstone OnDemand**设置**部分中，更改**预配状态**到**上**。

    ![Cornerstone OnDemand 预配状态](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. 定义的用户或组所需预配到 Cornerstone OnDemand。 在中**设置**部分中，选择在所需的值**作用域**。

    ![Cornerstone OnDemand 作用域](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 准备好预配后，选择**保存**。

    ![Cornerstone OnDemand 保存](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

此操作会启动初始同步所有用户或组中定义**作用域**中**设置**部分。 初始同步长，若要执行的时间比更高版本的同步。 只要 Azure AD 预配服务运行，它们会大约每隔 40 分钟。 

可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告。 报告描述了预配服务对 Cornerstone OnDemand 的 Azure AD 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

Cornerstone OnDemand“位置”特性需要一个与 Cornerstone OnDemand 门户上的角色相对应的值。 若要获取的有效列表**位置**值，请转到**编辑用户记录 > 组织结构 > 位置**Cornerstone OnDemand 门户中。

![Cornerstone OnDemand 预配编辑用户记录](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 预配位置](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 预配位置列表](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
