---
title: 教程：配置 Oracle 云基础结构控制台以便通过 Azure Active Directory 进行自动用户预配 |Microsoft Docs
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 665e4870619751bbda062473d0c2549b26352d94
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361596"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Oracle 云基础结构控制台

本教程介绍了需要在 Oracle 云基础结构控制台和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置后，Azure AD 使用 Azure AD 预配服务自动设置用户和组，并将其预配到 [Oracle 云基础结构控制台](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Oracle 云基础结构控制台中创建用户
> * 如果用户不需要访问，请在 Oracle 云基础结构控制台中删除用户
> * 使用户属性在 Azure AD 和 Oracle 云基础结构控制台之间保持同步
> * 在 Oracle 云基础结构控制台中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) 到 Oracle 云基础结构控制台 (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* Oracle 云基础结构控制 [租户](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)。
* 具有管理员权限的 Oracle 云基础结构控制中的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 和 Oracle 云基础结构控制台之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Oracle 云基础结构控制台以支持 Azure AD 的预配

1. 登录到 Oracle 云基础结构控制台的管理门户。 在屏幕的左上角，导航到 " **标识" > "联合**"。

    ![Oracle 管理员](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. 在 "Oracle 标识云服务控制台" 旁的页面上单击显示的 URL。

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. 单击 " **添加标识提供程序** " 以创建新的标识提供者。 保存要用作租户 URL 的一部分的 IdP id。单击 " **应用程序** " 选项卡旁边的加号图标创建 OAuth 客户端，并授予 IDCS 标识 "域管理员" AppRole。

    ![Oracle 云图标](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 请按照下面的屏幕截图来配置你的应用程序。 完成配置后，单击 " **保存**"。

    ![Oracle 配置](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle 令牌颁发策略](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. 在应用程序的 "配置" 选项卡下，展开 " **常规信息** " 选项以检索客户端 ID 和客户端密码。

    ![Oracle 令牌生成](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. 若要生成机密令牌，请使用 " **客户端 id：客户端密码**" 格式对客户端 id 和客户端密码进行 Base64 编码。 保存机密令牌。 此值将输入到 Azure 门户中 "Oracle 云基础结构" 控制台应用程序 "预配" 选项卡的 " **机密令牌** " 字段中。

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Oracle 云基础结构控制台

从 Azure AD 应用程序库添加 Oracle 云基础结构控制台，开始管理预配到 Oracle 云基础结构控制台。 如果以前为 SSO 设置了 Oracle 云基础结构控制台，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 Oracle 云基础结构控制台时，必须选择 "默认" **访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>步骤 5。 配置 Oracle 云基础结构控制台的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>若要为 Azure AD 中的 Oracle 云基础结构控制台配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Oracle Cloud Infrastructure Console”****。

    ![应用程序列表中的 "Oracle 云基础结构" 控制台链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入格式为的 **租户 URL** `https://<IdP ID>.identity.oraclecloud.com/admin/v1` 。 例如，`https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`。 在“机密令牌”中，输入之前检索到的机密令牌值。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 Oracle 云基础结构控制台。 如果连接失败，请确保你的 Oracle 云基础结构控制台帐户具有管理员权限，然后重试。

    ![屏幕截图显示 "管理员凭据" 对话框，你可以在其中输入租户 U R L 和机密令牌。](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 Oracle 云基础结构控制台**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Oracle 云基础结构控制台的用户属性。 选为 " **匹配** " 属性的属性用于匹配 "Oracle 云基础结构控制台" 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 Oracle 云基础结构控制台 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |userName|字符串|
      |活动|Boolean|
      |title|字符串|
      |emails[type eq "work"].value|字符串|
      |preferredLanguage|字符串|
      |name.givenName|字符串|
      |name.familyName|字符串|
      |addresses[type eq "work"].formatted|字符串|
      |addresses[type eq "work"].locality|字符串|
      |addresses[type eq "work"].region|字符串|
      |addresses[type eq "work"].postalCode|字符串|
      |addresses[type eq "work"].country|字符串|
      |addresses[type eq "work"].streetAddress|字符串|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|字符串|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|字符串|
      |urn： ietf： params： scim：架构： oracle： idcs： extension： user： User： bypassNotification|布尔|
      |urn： ietf： params： scim：架构： oracle： idcs： extension： user： User： isFederatedUser|布尔|

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步到 Oracle 云基础结构控制台**"。

11. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Oracle 云基础结构控制台的组属性。 选为 " **匹配** " 属性的属性用于匹配 "Oracle 云基础结构" 控制台中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要启用 "用于 Oracle 云基础结构的 Azure AD 预配服务" 控制台，请在 "**设置**" 部分中将设置**状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Oracle 云基础结构控制台的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
* 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
