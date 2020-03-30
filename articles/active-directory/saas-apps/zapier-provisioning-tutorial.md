---
title: 教程：使用 Azure 活动目录配置 Zapier 以自动预配用户 |微软文档
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户到 Zapier。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 66e224f9-2311-4564-bb84-99fce59a398f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: Zhchia
ms.openlocfilehash: 4091e4fd544dbc6450bc14bd0e0731c4d3024592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76992152"
---
# <a name="tutorial-configure-zapier-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Zapier

本教程介绍在 Zapier 和 Azure 活动目录 （Azure AD） 中配置自动用户预配所需的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配和取消向[Zapier](https://zapier.com/pricing)预配用户和组。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 在扎皮尔创建用户
> * 在 Zapier 中不再需要访问权限时删除用户
> * 使用户属性在 Azure AD 和 Zapier 之间保持同步
> * 扎皮尔的预配组和组成员身份
> * 扎皮尔的单次登录（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* Zapier 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 确定要在[Azure AD 和 Zapier 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-zapier-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Zapier 以支持使用 Azure AD 进行预配

1. 登录到您的[扎皮尔管理控制台](https://zapier.com/app/login/)。 导航到租户 ID 下的 **"设置**"。

    ![扎皮尔管理控制台](media/zapier-provisioning-tutorial/admin.png)

2. 在 **"公司设置"下**，选择**用户预配**。

    ![扎皮尔添加SCIM](media/zapier-provisioning-tutorial/user.png)

3. 复制**SCIM 基本 URL**和**SCIM 承载令牌**。 这些值将分别在 Azure 门户中的 Zapier 应用程序的预配选项卡中输入租户 URL 和秘密令牌字段。

    ![扎皮尔创建令牌](media/zapier-provisioning-tutorial/token.png)

## <a name="step-3-add-zapier-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加扎皮尔

从 Azure AD 应用程序库添加 Zapier，开始管理对 Zapier 的预配。 如果您以前为 SSO 设置了 Zapier，则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配给 Zapier 时，必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zapier"></a>步骤 5。 配置自动用户预配到扎皮尔 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-zapier-in-azure-ad"></a>要在 Azure AD 中配置 Zapier 的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

   ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**扎皮尔**。

   !["应用程序"列表中的扎皮尔链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

   ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

   ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理员凭据"** 部分下，输入您的 Zapier**租户 URL**和**秘密令牌**。 单击 **"测试连接**"以确保 Azure AD 可以连接到扎皮尔。 如果连接失败，请确保您的 Zapier 帐户具有管理员权限，然后重试。

   ![预配](./media/zapier-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

   ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到扎皮尔**。

9. 在**属性映射**部分中查看从 Azure AD 同步到 Zapier 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Zapier 中的用户帐户以进行更新操作。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Zapier API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

   |变量|类型|
   |---|---|
   |userName|String|
   |活动|Boolean|
   |externalId|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录组同步到扎皮尔**。

11. 在**属性映射**部分中查看从 Azure AD 同步到 Zapier 的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Zapier 中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    |变量|类型|
    |---|---|
    |displayName|String|
    |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Zapier 启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Zapier 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将开始 **"设置"** 部分中 **"范围**"中定义的所有用户和组的初始同步周期。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务运行，则大约每 40 分钟执行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源监视部署：

- 使用[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)确定已成功预配或未成功预配哪些用户
- 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看预配周期的状态以及预配周期与完成有多近
- 如果预配配置似乎处于不正常状态，则应用程序将进入隔离状态。 在此处了解有关隔离状态的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
