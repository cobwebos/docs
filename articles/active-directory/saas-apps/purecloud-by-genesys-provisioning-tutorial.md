---
title: 教程：将 PureCloud by Genesys 配置为使用 Azure Active Directory 进行自动用户预配 |Microsoft Docs
description: 了解如何自动预配用户帐户并取消其预配，Azure AD 通过 Genesys 到 PureCloud。
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 8917343175dd5ed8f82c1929683de98034323163
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321909"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>教程：为 PureCloud by Genesys 配置自动用户预配

本教程介绍了需要在 PureCloud by Genesys 和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置时，Azure AD 会使用 Azure AD 预配服务自动预配用户和组，并将其预配为 [PureCloud By Genesys](https://www.genesys.com) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 PureCloud by Genesys 中创建用户
> * 如果用户不需要访问，请在 PureCloud by Genesys 中删除用户
> * 使用户属性在 Azure AD 与 PureCloud 之间保持同步 Genesys
> * 在 PureCloud by Genesys 中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) 到 PureCloud by Genesys (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* PureCloud [组织](https://help.mypurecloud.com/?p=81984)。
* 有 [权](https://help.mypurecloud.com/?p=24360) 创建 Oauth 客户端的用户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定 [Genesys Azure AD 和 PureCloud 之间映射的](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)数据。 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>步骤 2. 将 PureCloud 配置为 Genesys，以支持使用 Azure AD 进行预配

1. 在 PureCloud 组织中创建配置的 [Oauth 客户端](https://help.mypurecloud.com/?p=188023) 。
2. [使用 oauth 客户端](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)生成令牌。
3. 如果你希望在 PureCloud 中自动设置组成员身份，则必须在 Azure AD 中的组中 [创建](https://help.mypurecloud.com/?p=52397) 具有相同名称的 PureCloud 中的组。

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 PureCloud by Genesys

从 Azure AD 应用程序库中添加 PureCloud by Genesys，开始将预配设置为 Genesys 的 PureCloud。 如果以前已将 PureCloud 设置为 SSO，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 PureCloud by Genesys 时，必须选择 " **默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>步骤 5。 通过 Genesys 配置 PureCloud 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>若要在 Azure AD 中配置 PureCloud by Genesys 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“PureCloud by Genesys”****。

    ![应用程序列表中的 PureCloud by Genesys 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分下，分别在 " **租户 URL** " 和 " **机密令牌** " 字段中输入 PureCloud By Genesys API URL 和 Oauth 令牌。 API URL 将 `{{API Url}}/api/v2/scim/v2` 使用 [PureCloud 开发人员中心](https://developer.mypurecloud.com/api/rest/index.html)的 PURECLOUD 区域的 api url 作为构造。 单击 " **测试连接** " 以确保 Azure AD 可以通过 Genesys 连接到 PureCloud。 如果连接失败，请确保 PureCloud by Genesys 帐户具有管理员权限，然后重试。

    ![预配](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步为 PureCloud by Genesys**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 PureCloud by Genesys 的用户属性。 选为 " **匹配** " 属性的属性用于匹配 PureCloud by Genesys 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 PureCloud BY Genesys API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

     |Attribute|类型|
     |---|---|
     |userName|字符串|
     |活动|Boolean|
     |displayName|字符串|
     |emails[type eq "work"].value|字符串|
     |title|字符串|
     |phoneNumbers[type eq "mobile"].value|字符串|
     |phoneNumbers[type eq "work"].value|字符串|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
     

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步为 PureCloud by Genesys**"。

11. 在 " **属性映射** " 部分中，查看通过 Genesys 从 Azure AD 同步到 PureCloud 的组属性。 选为 " **匹配** " 属性的属性用于匹配 PureCloud by Genesys 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。 PureCloud by Genesys 不支持组创建或删除，并且仅支持更新组。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 PureCloud by Genesys 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 PureCloud 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
* 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)。

## <a name="change-log"></a>更改日志

09/10-添加了对企业属性 "employeeNumber" 的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
