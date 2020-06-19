---
title: 教程：使用 Azure Active Directory 为 Hootsuite 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Hootsuite 及如何解除预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fbbee24e-272f-4fa6-819c-10c548bf0215
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: 1adbb0961ab610db936107f2fff210f4acecf2ed
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882970"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>教程：为 Hootsuite 配置自动用户预配

本教程介绍了在 Hootsuite 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Hootsuite](https://hootsuite.com/) 以及将其解除预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Hootsuite 中创建用户
> * 当用户不再需要访问权限时，在 Hootsuite 中删除用户
> * 使用户属性在 Azure AD 与 Hootsuite 之间保持同步
> * 在 Hootsuite 中预配组和组成员身份
> * 以[单一登录方式登录](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)到 Hootsuite（建议）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 在组织中拥有“管理成员”权限的 [Hootsuite](http://www.hootsuite.com/) 用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要[在 Azure AD 与 Hootsuite 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Hootsuite 以支持通过 Azure AD 进行预配

与 dev.support@hootsuite.com 联系，以获取在后续步骤中需要使用的长生存期机密令牌。 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 Hootsuite

从 Azure AD 应用程序库中添加 Hootsuite，开始管理到 Hootsuite 的预配。 如果你以前为 Hootsuite 设置过 SSO，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配到应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 Hootsuite 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>步骤 5。 配置到 Hootsuite 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>若要在 Azure AD 中为 Hootsuite 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![“所有应用程序”边栏选项卡](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. 在应用程序列表中，选择“Hootsuite”。

    ![“应用程序”列表中的“Hootsuite”链接](common/all-applications.png)

3. 选择“预配”选项卡。单击“入门”。

    ![预配选项卡](common/provisioning.png)

    ![“开始”边栏选项卡](./media/hootsuite-provisioning-tutorial/get-started.png)

4. 将“预配模式”设置为“自动”。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，在“租户 URL”中输入 `https://platform.hootsuite.com/scim/v2`。 输入前面在“步骤 2”中检索到的长生存期机密令牌值。 单击“测试连接”以确保 Azure AD 可以连接到 Hootsuite。 如果连接失败，请确保 Hootsuite 帐户具有管理员权限，然后重试。

    ![预配](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“预配 Azure Active Directory 用户” 。

9. 在“特性映射”部分中，查看从 Azure AD 同步到 Hootsuite 的用户特性。 选为“匹配”属性的特性用于匹配 Hootsuite 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Hootsuite API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |emails[type eq "work"].value|字符串|
   |活动|Boolean|
   |displayName|字符串|
   |preferredLanguage|字符串|
   |timezone|字符串|
   |urn:ietf:params:scim:schemas:extension:Hootsuite:2.0:User:organizationIds|字符串|
   |urn:ietf:params:scim:schemas:extension:Hootsuite:2.0:User:teamIds|字符串|

10. 若要为 Hootsuite 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“打开” 

    ![预配状态已打开](common/provisioning-toggle-on.png)

11. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Hootsuite 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

12. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
* 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。  


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
