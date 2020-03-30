---
title: 教程：使用 Azure Active Directory 为 Workplace by Facebook 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603204"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教程：为 Workplace by Facebook 配置自动用户预配

本教程介绍需要通过 Facebook 和 Azure 活动目录 （Azure AD） 在工作场所中执行以配置自动用户预配的步骤。 配置后，Azure AD 使用 Azure AD 预配服务，[通过 Facebook](https://work.workplace.com/)自动预配和取消向工作区预配用户和组。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>通过 Facebook 应用程序迁移到新的工作场所
如果您通过 Facebook 与工作场所的现有集成，请参阅以下部分，了解即将进行的更改。 如果您是首次通过 Facebook 设置工作区，则可以跳过此部分并转到支持的功能。 

#### <a name="whats-changing"></a>有什么变化？
* Azure AD 端的更改：在工作场所中预配用户的授权方法历来是一个长期使用的秘密令牌。 很快，您将看到授权方法更改为 OAuth 授权授予。 
* 工作场所方面的更改：以前，Azure AD 应用是 Facebook 在工作场所中的自定义集成。 现在，您将在工作区集成目录中看到 Azure AD 作为第三方应用程序。 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>需要执行什么操作才能将现有自定义集成迁移到新应用程序？
如果您有具有有效令牌的现有工作区集成，**则无需执行任何操作**。 我们每周会自动将客户迁移到新应用程序。 这是完全在幕后完成的。 如果无法等待并希望手动移动到新应用程序，则可以从库中添加新的工作区实例，然后重新配置预配。 工作场所的所有新实例将自动使用新的应用程序版本。 

 
如果您的工作场所集成处于隔离状态，您需要再次提供有效的令牌，以便我们迁移您。 管理员凭据部分将灰显，但您可以追加以下内容 **（？Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride_true**） 到 URL 以再次保存凭据。 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判断我的应用程序是否已迁移？ 
迁移应用程序时，将删除有关向上通信更改的授权部分中的横幅，并将机密令牌字段替换为蓝色授权按钮。 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>我的应用程序上已灰显了管理员凭据部分，无法保存。 为什么？
我们已锁定现有工作场所客户的管理凭据部分。 将租户迁移到新的工作区应用程序后，您将能够再次更新管理员凭据部分。 如果无法等待，可以使用上面的 URL 编辑应用程序。 

 
#### <a name="when-will-these-changes-happen"></a>这些变化何时发生？
工作场所的所有新实例都将使用新的集成/授权方法。 现有集成将在 5 月前逐步迁移。 工作场所团队将截止日期从 2 月 28 日延长至 5 月 1 日。 

## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 通过 Facebook 在工作场所创建用户
> * 当 Facebook 不再需要访问权限时，通过 Facebook 删除用户
> * 通过 Facebook 使 Azure AD 和工作区之间的用户属性保持同步
> * Facebook 单[一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial)工作场所（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）
* 已启用 Workplace by Facebook 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 通过 Facebook 确定要[在 Azure AD 和工作区之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>步骤 2. 按 Facebook 配置工作区以支持使用 Azure AD 进行预配

在配置和启用预配服务前，需确定 Azure AD 中哪些用户和/或组表示需要访问 Workplace by Facebook 应用的用户。 确定后，可按照此处的说明将这些用户分配到你的 Workplace by Facebook 应用：

*   建议将单个 Azure AD 用户分配到 Workplace by Facebook 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Workplace by Facebook 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库通过 Facebook 添加工作区

从 Azure AD 应用程序库添加 Facebook 的工作区，以开始管理由 Facebook 预配到工作区。 如果您以前在 Facebook 为 SSO 设置工作区，则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 当通过 Facebook 将用户和组分配给工作区时，您必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Workplace by Facebook”****。

    ![应用程序列表中的 Workplace by Facebook 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下，单击 **"授权**"。 您将通过 Facebook 的授权页面重定向到工作场所。 通过 Facebook 用户名输入您的工作场所，然后单击"**继续"** 按钮。 单击 **"测试连接**"以确保 Azure AD 可以通过 Facebook 连接到工作场所。 如果连接失败，请确保您的"Facebook"帐户的工作区具有管理员权限，然后重试。

    ![预配](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![授权](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。****

8. 在 **"映射"** 部分下，选择**通过 Facebook 将 Azure 活动目录用户同步到工作区**。

9. 在**属性映射**部分中查看 Facebook 从 Azure AD 同步到工作区的用户属性。 选为“匹配”属性的属性将用于匹配 Workplace by Facebook 中的用户帐户以执行更新操作****。 如果您选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Facebook API 的工作区支持根据该属性筛选用户。 选择“保存”按钮以提交任何更改****。

   |特性|类型|
   |---|---|
   |userName|String|
   |displayName|String|
   |活动|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |名称.格式化|String|
   |地址[类型 eq"工作"]格式化|String|
   |addresses[type eq "work"].streetAddress|String|
   |地址_类型 eq"工作"\局部性|String|
   |地址_类型 eq"工作"\区域|String|
   |地址[类型 eq"工作"\国家/地区|String|
   |addresses[type eq "work"].postalCode|String|
   |地址[类型 eq"其他"]格式化|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：经理|String|
   |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|String|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要启用 Facebook 为工作区启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义您希望通过 Facebook 预配到工作区的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将开始 **"设置"** 部分中 **"范围**"中定义的所有用户和组的初始同步周期。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务运行，则大约每 40 分钟执行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源监视部署：

1. 使用[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)确定已成功预配或未成功预配哪些用户
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看预配周期的状态以及预配周期与完成有多近
3. 如果预配配置似乎处于不正常状态，则应用程序将进入隔离状态。 在此处了解有关隔离状态的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>故障排除提示
*  如果您看到用户未成功创建，并且存在代码为"1789003"的审核日志事件，则表示用户来自未验证的域。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
