---
title: 教程：配置新的遗物（限量版本），以便使用 Azure 活动目录自动预配用户 |微软文档
description: 了解如何自动预配和取消将用户帐户从 Azure AD 预配到新遗物（限量版本）。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727341"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>教程：为自动用户预配配置新遗物（有限版本）

本教程介绍需要在新遗物（有限版本）和 Azure 活动目录 （Azure AD） 中执行以配置自动用户预配的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配和取消向[新遗物](https://newrelic.com/)预配和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 在新遗物中创建用户（限量发行）
> * 删除新遗物（有限版本）中的用户，当他们不再需要访问权限时
> * 使用户属性在 Azure AD 和新遗物（有限版本）之间保持同步
> * 在新遗物（限量发行）中预配组和组成员身份
> * [单次登录](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)新遗物（限量发行）（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 您希望用户有权访问的新遗物（有限版本）的一个或多个帐户。 

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 确定要在[Azure AD 和新遗物（有限版本）之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置新遗物（有限版本）以支持使用 Azure AD 进行预配

与您的客户代表合作，或在support.newrelic.com获得支持，为您的组织配置 SCIM 和 SSO。 您需要向您的客户代表提供：

- 您的组织名称
- 要与组织关联的新遗物帐户帐户指示列表

通过此信息，您的客户代表在我们的新系统中为您创建组织记录，并将您的帐户关联到组织。

您的帐户代表为您提供以下信息，您需要为您的身份提供商配置新遗物 SCIM/SSO 应用程序：

- SCIM 终结点（租户 URL）
- SCIM 无记名令牌（秘密令牌）

SCIM 承载令牌允许在新遗物中预配您的用户，因此请保持该值的安全。 您的帐户代表将以安全的方式将 SCIM 无记名令牌转让给您。

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加新遗物（限量版本）

从 Azure AD 应用程序库添加新遗物（有限版本），以开始管理新遗物（有限版本）的预配。 如果您以前为 SSO 设置了新遗物（限量版本），则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配给"新遗物"（有限版本）时，必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>步骤 5。 配置自动用户预配到新遗物（有限版本） 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>要在 Azure AD 中配置新遗物（有限版本）的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 **"新遗物（限量发行）"。**

    ![应用程序列表中的 New Relic 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://scim-provisioning.service.newrelic.com/scim/v2`，输入租户 URL。 输入在**秘密令牌**中检索到的 SCIM 身份验证令牌值。 单击 **"测试连接**"以确保 Azure AD 可以连接到新遗物。 如果连接失败，请确保您的新遗物帐户具有管理员权限，然后重试。

    ![预配](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。 

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到新遗物（限量版本）。**

9. 在**属性映射**部分中查看从 Azure AD 同步到新遗物（有限版本）的用户属性。 选择为 **"匹配**属性"的属性用于匹配更新操作的新 Relic（有限版本）中的用户帐户。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保新遗物（限释放）API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |externalId|字符串|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |名称.格式化|字符串|
   |timezone|字符串|

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录组同步到新遗物（限量版本）。**

11. 查看**属性映射**部分中从 Azure AD 同步到新遗物（有限版本）的组属性。 选择为 **"匹配**属性"的属性用于匹配更新操作的新遗物（有限版本）中的组。 选择“保存”按钮以提交任何更改****。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要启用新遗物的 Azure AD 预配服务（受限版本），在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围**"中所需的值，定义要预配到新遗物（有限版本）的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将开始 **"设置"** 部分中 **"范围**"中定义的所有用户和组的初始同步周期。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务运行，则大约每 40 分钟执行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源监视部署：

* 使用[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)确定已成功预配或未成功预配哪些用户
* 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看预配周期的状态以及预配周期与完成有多近
* 如果预配配置似乎处于不正常状态，则应用程序将进入隔离状态。 在此处了解有关隔离状态的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
