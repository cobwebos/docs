---
title: 教程：使用 Azure 活动目录配置朱诺旅程，以便自动预配用户 |微软文档
description: 了解如何自动预配和取消预配用户帐户从 Azure AD 到朱诺旅程。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641844"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>教程：为自动用户预配配置朱诺旅程

本教程介绍在 Juno 旅程和 Azure 活动目录 （Azure AD） 中配置自动用户预配所需的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配和取消向[Juno 旅程](https://www.junojourney.com/)预配和取消预配用户和组。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 在朱诺旅程中创建用户
> * 在朱诺旅程中删除用户，当他们不再需要访问权限时
> * 使用户属性在 Azure AD 和 Juno 旅程之间保持同步
> * [朱诺之旅的单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
*  [朱诺之旅租户](https://www.junojourney.com/getstartedwithjuno)。
*  具有管理员权限的 Juno 旅程中的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 确定[要在 Azure AD 和 朱诺行程之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>步骤 2. 将朱诺旅程配置为支持使用 Azure AD 进行预配

1. 有关**秘密令牌**和**租户 URL，** 请联系 Junosupport@the-juno.com旅程支持团队。 此值将分别在 Azure 门户中的 Juno 旅程应用程序的"预配"选项卡中输入 **。"** **Tenant URL** 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加朱诺旅程

从 Azure AD 应用程序库添加朱诺旅程，开始管理朱诺旅程的预配。 如果您以前为 SSO 设置了 Juno 旅程，则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配给 Juno 旅程时，必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>步骤 5。 配置到朱诺旅程的自动用户预配 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>要在 Azure AD 中为朱诺旅程配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Juno Journey”。****

    !["应用程序"列表中的朱诺旅程链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下，输入在**租户 URL**中检索的租户 URL 值。 输入在**秘密令牌**中检索到的秘密令牌值。 单击 **"测试连接**"以确保 Azure AD 可以连接到朱诺旅程。 如果连接失败，请确保您的 Juno 旅程帐户具有管理员权限，然后重试。

    ![预配](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。 

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到朱诺旅程**。

9. 在**属性映射**部分中查看从 Azure AD 同步到朱诺旅程的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Juno 旅程中的用户帐户以进行更新操作。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Juno 旅程 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

   |变量|类型|
   |---|---|
   |userName|字符串|
   |externalId|字符串|
   |displayName|字符串|
   |title|字符串|
   |活动|Boolean|
   |preferredLanguage|字符串|
   |emails[type eq "work"].value|字符串|
   |地址[类型 eq"工作"\国家/地区|字符串|
   |地址_类型 eq"工作"\区域|字符串|
   |地址_类型 eq"工作"\局部性|字符串|
   |addresses[type eq "work"].postalCode|字符串|
   |地址[类型 eq"工作"]格式化|字符串|
   |addresses[type eq "work"].streetAddress|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |名称.中间名称|字符串|
   |名称.格式化|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|字符串|
   |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：员工编号|字符串|
   |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：成本中心|字符串|
   urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|字符串|
   urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：经理|字符串|
   urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：组织|字符串|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要启用朱诺行程的 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Juno 旅程的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

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
