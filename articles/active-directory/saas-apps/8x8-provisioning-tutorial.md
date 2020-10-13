---
title: 教程：使用 Azure Active Directory 为 8x8 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 8x8 及如何取消预配。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 850db53cbf88968a413d72527d76463e17aa8f5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361698"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>教程：为 8x8 配置自动用户预配

本教程介绍了在 8x8 Configuration Manager 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [8x8](https://www.8x8.com) 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 8x8 中创建用户
> * 在用户不再有访问需求的情况下，在 8x8 中删除用户
> * 使用户属性在 Azure AD 和 8x8 之间保持同步
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial)到 8x8（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* 任何级别的 8x8 X 系列订阅。
* 在 [Configuration Manager](https://vo-cm.8x8.com) 中具有管理员权限的 8x8 用户帐户。
* 已配置[使用 Azure AD 进行单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial)。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定[在 Azure AD 与 8x8 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 8x8 以支持通过 Azure AD 进行预配

本部分中的步骤将指导你配置 8x8 以支持通过 Azure AD 进行预配。

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>要在 8x8 Configuration Manager 中配置用户预配访问令牌，请执行以下操作：

1. 登录到 [Configuration Manager](https://vo-cm.8x8.com)。 选择“标识管理”。

   ![标识管理](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. 单击“显示用户预配信息”链接以生成令牌。

   ![显示用户预配](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. 复制 8x8 URL 和 8x8 API 令牌值 。 在 Azure 门户的 8x8 应用程序的“预配”选项卡中，将这些值分别输入“租户 URL”和“机密令牌”字段 。

   ![复制 URL 和令牌](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 8x8

从 Azure AD 应用程序库添加 8x8，开始管理到 8x8 的预配。 如果以前为 SSO 设置过 8x8，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 这一选项较为简单，大多数人都在使用。

如果选择仅根据用户或组的属性来定义谁在预配范围内，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 8x8 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>步骤 5。 配置到 8x8 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 8x8 中创建、更新以及禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>要在 Azure AD 中为 8x8 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![“所有应用程序”边栏选项卡](./media/8x8-provisioning-tutorial/all-applications.png)

2. 在“应用程序”列表中选择“8x8”。

    ![“应用程序”列表中的 8x8 链接](common/all-applications.png)

3. 选择“预配”选项卡。单击“入门”。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

   ![“开始”边栏选项卡](./media/8x8-provisioning-tutorial/get-started.png)

4. 将“预配模式”设置为“自动”。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，将 8x8 URL 从 Configuration Manager 复制到“租户 URL”  。 将 8x8 API 令牌从 Configuration Manager 复制到“机密令牌” 。 单击“测试连接”以确保 Azure AD 可以连接到 8x8。 如果连接失败，请确保 8x8 帐户具有管理员权限，然后重试。

    ![屏幕截图显示 "管理员凭据" 对话框，你可以在其中输入租户 U R L 和机密令牌。](./media/8x8-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“预配 Azure Active Directory 用户” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 8x8 的用户属性。 选为“匹配”属性的特性用于匹配 8x8 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 8x8 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|说明|
   |---|---|---|
   |userName|字符串|设置用户名和联合 ID|
   |externalId|字符串||
   |活动|Boolean||
   |title|字符串||
   |emails[type eq "work"].value|字符串||
   |name.givenName|字符串||
   |name.familyName|字符串||
   |phoneNumbers[type eq "mobile"].value|字符串|个人联系电话|
   |phoneNumbers[type eq "work"].value|字符串|个人联系电话|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|字符串|用户创建后无法更新|
   |区域设置|字符串|默认情况下未映射|
   |timezone|字符串|默认情况下未映射|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 8x8 启用 Azure AD 预配服务，请在“设置”部分将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 8x8 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败。
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)以查看预配周期的状态以及完成进度。
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
