---
title: 教程：可用于实现时差的用户设置-Azure AD
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Slack 和取消其预配。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b7fa5aea835329be8f65a3bb1775ba5b0d97d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389848"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>教程：为 Slack 配置自动用户预配

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 Slack 和取消其预配所需在 Slack 和 Azure 中执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 用时差创建用户
> * 如果用户不再需要访问，请将其删除
> * 使用户属性在 Azure AD 和时差之间保持同步
> * 在时差中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)到时差（建议）


## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* 启用了[Plus 计划](https://aadsyncfabric.slack.com/pricing)或更佳计划的可宽延时间租户。
* 具有团队管理员权限的具有时差的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定[Azure AD 和时差之间要映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>步骤 2。 添加 Azure AD 应用程序库中的时差

添加 Azure AD 应用程序库中的时差，开始管理预配到时差。 如果以前为 SSO 设置了时差，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>步骤 3. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到时差时，必须选择 "**默认" 访问权限**以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>步骤 4. 配置自动用户预配到时差 

本部分指导完成将 Azure AD 连接到 Slack 的用户帐户预配 API 和配置预配服务，以便在 Slack 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>若要在 Azure AD 中为 Slack 配置自动用户帐户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Slack”。

    ![应用程序列表中的 Slack 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”**** 部分下，单击“授权”****。 这会在新的浏览器窗口中打开“Slack 授权”对话框。

    ![授权](media/slack-provisioning-tutorial/authorization.png)


6. 在新窗口中，使用团队管理员帐户登录到 Slack。 在显示的授权对话框中，选择要启用预配的 Slack 团队，并选择“授权”****。 完成后，返回到 Azure 门户完成预配配置。

    ![授权对话框](./media/slack-provisioning-tutorial/slackauthorize.png)

7. 在 Azure 门户中，单击“测试连接”**** 以确保 Azure AD 可以连接到 Slack 应用。 如果连接失败，请确保 Slack 帐户具有团队管理员权限，并重试“授权”步骤。

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Slack”****。

11. 在“属性映射”**** 部分中，查看将从 Azure AD 同步到 Slack 的用户属性。 请注意，选为**匹配**属性的属性用于匹配 Slack 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |externalId|String|
   |displayName|String|
   |name.familyName|String|
   |name.givenName|字符串|
   |title|字符串|
   |emails[type eq "work"].value|String|
   |userName|String|
   |昵称|String|
   |地址 [type eq "非类型化"]. streetAddress|String|
   |地址 [类型 eq "非类型化"]。位置|String|
   |地址 [type eq "非类型化的"]。区域|String|
   |地址 [type eq "非类型化"]|String|
   |地址 [类型 eq "非类型化"]。国家/地区|String|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "work"].value|String|
   |role [主 eq "True"]。值|String|
   |区域设置|字符串|
   |名称. honorificPrefix|String|
   |照片 [type eq "photo"]。值|String|
   |profileUrl|String|
   |timezone|String|
   |userType|String|
   |urn： scim：架构：扩展： enterprise： 1.0. 部门|String|
   |urn： scim：架构：扩展： enterprise： 1.0. manager|参考|
   |urn： scim：架构：扩展： enterprise： 1.0. employeeNumber|String|
   |urn： scim：架构：扩展： enterprise： 1.0. costCenter|String|
   |urn： scim：架构：扩展： enterprise： 1.0. 组织|String|
   |urn： scim：架构：扩展： enterprise：1。0|String|

12. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到时差**"。

13. 在“属性映射”**** 部分中，查看将从 Azure AD 同步到 Slack 的组属性。 请注意，选为**匹配**属性的属性用于在更新操作中匹配 Slack 中的组。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|String|
      |members|参考|

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 要为 Slack 启用 Azure AD 预配服务，请在“设置”**** 部分中将“预配状态”更改**** 为“启用”****

    ![预配状态已打开](common/provisioning-toggle-on.png)

16. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到时差的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

17. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-5-monitor-your-deployment"></a>步骤 5. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="connector-limitations"></a>连接器限制

* 配置 Slack 的 **displayName** 属性时，请注意以下行为：

  * 值并非完全唯一（例如，2 个用户可以具有相同的显示名称）

  * 支持非英语字符、空格、大写。 
  
  * 允许使用的标点符号包括句点、下划线、连字符、撇号、括号（例如 ( [ { } ] )****）和分隔符（例如 , / ;****）。
  
  * 仅当在 Slack 的工作区/组织中配置了这两个设置时才进行更新 - **启用配置文件同步**和**用户无法更改其显示名称**。
  
* Slack 的 **userName** 属性必须小于 21 个字符并具有唯一值。

* 时差仅允许与属性 "**用户名**" 和 "**电子邮件**" 匹配。  

## <a name="change-log"></a>更改日志

* 06/16/2020-修改后的 DisplayName 属性仅在创建新用户期间更新。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)