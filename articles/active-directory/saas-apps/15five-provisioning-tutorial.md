---
title: 教程：为15Five 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到15Five 以及取消其预配。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 40fd048e1ace56af7a9198088d6129925033be93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331210"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>教程：为15Five 配置自动用户预配

本教程的目的是演示要在15Five 和 Azure Active Directory (Azure AD) 中执行的步骤，以将 Azure AD 自动预配和取消预配到 [15Five](https://www.15five.com/pricing/)。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配。

> [!NOTE]
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在15Five 中创建用户
> * 当用户不再需要访问权限时，删除15Five 中的用户
> * 使用户属性在 Azure AD 和15Five 之间保持同步
> * 在15Five 中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/15five-tutorial) 到 15Five (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 。
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [15Five 租户](https://www.15five.com/pricing/)。
* 15Five 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 与15Five 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置15Five 以支持 Azure AD 的预配

将15Five 配置为使用 Azure AD 进行自动用户预配之前，需要在15Five 上启用 SCIM 设置。

1. 登录到 [15Five 管理控制台](https://my.15five.com/)。 导航到 **功能 > 集成**。

    ![15Five 管理控制台](media/15five-provisioning-tutorial/integration.png)

2.  单击 " **SCIM 2.0**"。

    ![15Five 管理控制台](media/15five-provisioning-tutorial/image00.png)

3.  导航到 **SCIM integration > 生成 OAuth 令牌**。

    ![15Five 添加 SCIM](media/15five-provisioning-tutorial/image02.png)

4.  复制 **SCIM 2.0 基 URL** 和 **访问令牌**的值。 此值将在 Azure 门户的15Five 应用程序的 "设置" 选项卡中的 " **租户 URL** " 和 " **机密令牌** " 字段中输入。
    
    ![15Five 添加 SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加15Five

从 Azure AD 应用程序库中添加15Five，开始管理预配到15Five。 如果以前为 SSO 设置了15Five，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到15Five 时，必须选择 " **默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>步骤 5。 配置15Five 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在15Five 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>若要在 Azure AD 中配置15Five 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“15Five”****。

    ![应用程序列表中的 15Five 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5.  在 "管理员凭据" 部分下，输入前面在 "**租户 URL** " 和 "**机密令牌**" 字段中检索到的**SCIM 2.0 基 URL 和访问令牌**值。 单击 " **测试连接** " 以确保 Azure AD 可以连接到15Five。 如果连接失败，请确保15Five 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 15Five**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到15Five 的用户属性。 选为 " **匹配** " 属性的特性用于匹配15Five 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。


   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |title|字符串|
   |emails[type eq "work"].value|字符串|
   |userName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn： ietf： params： scim：架构：扩展：15Five：2.0：用户：位置|String|
   |urn： ietf： params： scim：架构：扩展：15Five：2.0：用户：开始日期|String|

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步到 15Five**"。

11. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到15Five 的组属性。 选为 " **匹配** " 属性的特性用于匹配15Five 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |externalId|字符串|
      |displayName|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为15Five 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到15Five 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)。  
    
## <a name="connector-limitations"></a>连接器限制

* 15Five 不支持用户的软删除。

## <a name="change-log"></a>更改日志

* 06/16/2020-添加了对企业扩展属性 "管理器" 和用户的自定义属性 "Location" 和 "开始日期" 的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户设置](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。
