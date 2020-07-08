---
title: 教程：为 Atlassian Cloud 配置 Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Atlassian Cloud 并取消其预配。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 95455d389669b89075ca0ea8583cc7858bb532f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317627"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>教程：为 Atlassian Cloud 配置自动用户预配

本教程的目的是演示要在 Atlassian 云和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到[Atlassian 云](https://www.atlassian.com/licensing/cloud)。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Atlassian Cloud 中创建用户
> * 如果用户不需要访问，请在 Atlassian 云中删除用户
> * 使用户属性在 Azure AD 和 Atlassian Cloud 之间保持同步
> * 在 Atlassian 云中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/atlassian-cloud-tutorial)到 Atlassian Cloud （推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [Atlassian 云租户](https://www.atlassian.com/licensing/cloud)
* Atlassian Cloud 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要[在 Azure AD 和 Atlassian Cloud 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Atlassian 云以支持 Azure AD 的预配

1. 导航到[Atlassian 组织管理器](https://admin.atlassian.com) **> 选择 "org > 目录"**。

    ![Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. 单击 "**用户设置**"，然后单击 "**创建目录**"。 在 Azure AD 门户中的 Atlassian 云应用程序的 "设置" 选项卡中，复制将在 "**租户 url** " 和 "**机密令牌**" 字段中输入的**目录基 URL**和**持有者令牌**。

    ![Atlassian 云预配 ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![ Atlassian 云预配 ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![ Atlassian 云预配](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Atlassian Cloud

从 Azure AD 应用程序库添加 Atlassian Cloud，开始管理预配到 Atlassian 云。 如果以前为 SSO 设置了 Atlassian 云，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 Atlassian Cloud 时，必须选择 "**默认" 访问权限**以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>步骤 5。 配置 Atlassian Cloud 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Atlassian Cloud 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>若要在 Azure AD 中配置 Atlassian Cloud 的自动用户预配：

1. 登录到[Azure 门户](https://portal.azure.com)，选择 "**企业应用程序**"，选择 "**所有应用程序**"，然后选择 " **Atlassian Cloud**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Atlassian Cloud”。 

    ![应用程序列表中的 Atlassian Cloud 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分中，输入先前从 Atlassian 云帐户中检索到的**租户 URL**和**机密令牌**。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Atlassian Cloud。 如果连接失败，请确保 Atlassian 云帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存” 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Atlassian Cloud**"。

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Atlassian Cloud 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 Atlassian Cloud 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|String|
   |活动|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |emails[type eq "work"].value|String|   

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 Atlassian Cloud**"。

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Atlassian Cloud 的组属性。 选为 "**匹配**" 属性的属性用于匹配 Atlassian Cloud 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|String|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Atlassian Cloud 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Atlassian Cloud 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

16. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。  

## <a name="connector-limitations"></a>连接器限制

* Atlassian Cloud 仅允许从[已验证的域](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)预配用户。
* Atlassian Cloud 目前不支持组重命名。 这意味着，对 Azure AD 中组的 displayName 的任何更改都不会在 Atlassian Cloud 中更新和反映。
* 仅当用户具有 Microsoft Exchange 邮箱时，才填充 Azure AD 中**邮件**用户属性的值。 如果用户没有用户，则建议将不同的所需属性映射到 Atlassian Cloud 中的**电子邮件**属性。

## <a name="change-log"></a>更改日志

* 06/15/2020-添加了组的批处理修补程序支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png