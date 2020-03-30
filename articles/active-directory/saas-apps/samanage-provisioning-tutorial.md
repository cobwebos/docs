---
title: 教程：使用 Azure Active Directory 为 Samanage 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure 活动目录配置为自动预配用户帐户并将其预配到 Samanage。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060423"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教程：为 Samanage 配置自动用户预配

本教程演示了在 Samanage 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和组预配到 Samanage。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用、工作方式以及常见问题的信息，请参阅[使用 Azure 活动目录 自动预配和取消预配软件即服务 （SaaS） 应用程序](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定您具有：

* Azure AD 租户。
* 具有专业包的[Samanage 租户](https://www.samanage.com/pricing/)。
* 具有管理员权限的 Sa 管理中的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于[Sa管理休息 API](https://www.samanage.com/api/)。 此 API 可用于具有专业包的帐户的 Samanage 开发人员。

## <a name="add-samanage-from-the-azure-marketplace"></a>从 Azure 应用商店添加管理

在使用 Azure AD 配置用于自动用户预配的 Samanage 之前，请将从 Azure 应用商店中的 Samanage 添加到托管 SaaS 应用程序列表中。

要从应用商店添加 Samanage，请按照以下步骤操作。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧的导航窗格中，选择**Azure 活动目录**。

    ![Azure 活动目录图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”****。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 **"管理"** 并从结果面板中选择 **"Sa管理**"。 要添加应用程序，请选择"**添加**"。

    ![结果列表中的 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>将用户分配给 Samanage

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问 Samanage。 要将这些用户或组分配给 Samanage，请按照将[用户或组分配给企业应用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-samanage"></a>将用户分配到 Samanage 的重要提示

*    如今，Samanage 角色会自动动态地填充在 Azure 门户 UI 中。 在将 Samanage 角色分配给用户之前，请确保针对 Samanage 完成初始同步，以检索 Samanage 租户中的最新角色。

*    我们建议您将单个 Azure AD 用户分配给 Samanage 以测试初始自动用户预配配置。 测试成功后，您可以稍后分配其他用户和组。

*    将用户分配给 Samanage 时，请在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configure-automatic-user-provisioning-to-samanage"></a>将自动用户预配配置为 Samanage

本节将指导您完成配置 Azure AD 预配服务的步骤。 使用它根据 Azure AD 中的用户或组分配创建、更新和禁用 Samanage 中的用户或组。

> [!TIP]
> 您还可以为 Samanage 启用基于 SAML 的单一登录。 按照[Sa管理单登录教程](samanage-tutorial.md)中的说明进行操作。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>为 Azure AD 中的 Sa 管理配置自动用户预配

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序** > **所有应用程序** > **Sa管理**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Samanage”****。

    ![应用程序列表中的 Sa管理链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![Samanage 预配](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下，输入您的 Samanage**租户 URL**和**秘密令牌**。 单击 **"测试连接**"以确保 Azure AD 可以连接到 Samanage。 如果连接失败，请确保 Samanage 帐户具有管理员权限，然后重试。

    ![管理测试连接](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 框中，输入接收预配错误通知的个人或组的电子邮件地址。 选中"**在发生故障时发送电子邮件通知**"复选框。

    ![管理通知电子邮件](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. 选择“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 Sa管理**。

    ![管理用户同步](./media/samanage-provisioning-tutorial/UserMappings.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Samanage 的用户属性****。 选为“匹配”属性的特性用于匹配 Samanage 中的用户帐户以执行更新操作****。 要保存任何更改，请选择"**保存**"。

    ![管理匹配的用户属性](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. 若要启用组映射，在“映射”部分下，选择“将 Azure Active Directory 组同步到 Samanage”********。

    ![Sa管理组同步](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. 将“启用”**** 设置为“是”**** 将同步组。 在“属性映射”部分中，查看从 Azure AD 同步到 Samanage 的组属性****。 选为“匹配”属性的特性用于匹配 Samanage 中的用户帐户以执行更新操作****。 要保存任何更改，请选择"**保存**"。

    ![管理匹配组属性](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 要配置范围筛选器，请按照[范围界定筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

13. 要为 Samanage 启用 Azure AD 预配服务，在 **"设置"** 部分中，将**预配状态**更改为**On**。

    ![管理预配状态](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. 定义要预配到 Samanage 的用户或组。 在 **"设置"** 部分中，在 **"范围**"中选择所需的值。 选择 **"同步所有用户和组**"选项时，请考虑以下"连接器限制"部分中所述的限制。

    ![管理范围](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. 准备好预配时，请选择"**保存**"。

    ![管理保存](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户或组的初始同步。 初始同步执行的时间比以后同步长。 只要 Azure AD 预配服务运行，它们大约每 40 分钟发生一次。 

您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Samanage 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

如果选择 **"同步所有用户和组**"选项并为 SaManage**角色**属性配置值，**则"默认值"下的值（可选）"** 框必须以以下格式表示：

- {"显示名称"："角色"，其中角色是您想要的默认值。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
