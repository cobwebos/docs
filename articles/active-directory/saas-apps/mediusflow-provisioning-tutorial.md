---
title: 教程：使用 Azure Active Directory 为 MediusFlow 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure AD 中自动向 MediusFlow 预配和取消预配用户帐户。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548066"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>教程：为 MediusFlow 配置自动用户预配

本教程介绍了在 MediusFlow 和 Azure Active Directory (Azure AD) 中配置自动用户预配所需执行的步骤。 配置后，Azure AD 就可以使用 Azure AD 预配服务自动向 [MediusFlow](https://www.mediusflow.com/) 预配和取消预配用户和组。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 MediusFlow 中创建用户
> * 在 MediusFlow 中删除不再需要访问的用户
> * 在 Azure AD 和 MediusFlow 之间同步用户特性
> * 在 MediusFlow 中预配组和组成员资格
> * 单一登录 MediusFlow（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中[有权](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 具有质量保证或生产租户的活动 MediusFlow 订阅。
* MediusFlow 中拥有管理员访问权限的用户帐户，可以在 MediusFlow 中执行配置。
* 在 MediusFlow 租户中添加的公司，应将用户预配到其中。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)内。
3. 确定[在 Azure AD 与 MediusFlow 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>步骤 2. 将 MediusFlow 配置为支持通过 Azure AD 进行预配

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>激活 MediusFlow 中的 Microsoft 365 应用
首先，在 MediusFlow 中启用 Azure AD 登录名的访问权限和 Azure AD 配置功能，具体步骤如下：

#### <a name="user-login"></a>用户登录
若要使登录流 Microsoft 365/Azure AD 请参阅 [此] (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) 文章。

#### <a name="user-transfer-configuration"></a>用户传输配置
若要启用用户的配置门户进行设置，请参阅 [此](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) 文 Azure AD。

#### <a name="configure-user-provisioning"></a>配置用户设置

1.  通过提供租户 ID 来登录 [MediusFlow 管理控制台](https://office365.cloudapp.mediusflow.com/)。

    ![Authenticate](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. 验证与 MediusFlow 的连接。

    ![Verify](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. 提供 Azure AD 租户 ID。

    ![提供租户 ID](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   若要了解如何找到它，可以详细阅读 [FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id)。

4. 保存配置。

    ![Authenticate](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. 选中“使用用户预配”，然后单击“确定”。

    ![Authenticate](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. 单击“生成密钥”。 复制并保存此值。将在 Azure 门户中 MediusFLow 应用的“预配”选项卡的“密钥令牌”字段中输入此值。

    ![Authenticate](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. 单击“确定”。

    ![Authenticate](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. 若要使用预定义角色集、公司和 MediusFlow 中的其他常规配置来导入用户，需要先对其进行配置。 首先，通过单击“添加新配置”来添加配置。

    ![配置用户](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. 提供用户的默认设置。 在此视图中，可以设置默认特性。 如果标准设置是正确的，那么只提供有效的公司名称就足够了。 由于这些配置设置是从 Mediusflow 中提取的，因此需要先对其进行配置。 有关详细信息，请参阅本文的“先决条件”部分。

    ![配置用户](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. 单击“保存”，以保存用户配置。

    ![配置用户](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. 若要获取用户预配链接，请单击“复制 SCIM 链接”。 复制并保存此值。 将在 Azure 门户中 MediusFLow 应用的“预配”选项卡的“租户 URL”字段中输入此值。
 
    ![配置用户](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用库添加 MediusFlow

从 Azure AD 应用库添加 MediusFlow，以开始管理向 MediusFlow 预配用户。 如果之前已经为 MediusFlow 设置了 SSO，那么可以使用相同的应用。 不过，建议在最初测试集成时单独创建一个应用。 若要详细了解如何从库中添加应用，可以单击[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围内 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择只根据用户或组的特性来限定谁在预配范围内，可以使用范围筛选器，如[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述。 

* 将用户和组分配到 MediusFlow 时，必须选择除“默认访问”之外的角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用上唯一可用的角色是“默认访问”角色，可以[更新应用清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)来添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来控制它。 如果预配范围设置为所有用户和组，可以指定[基于特性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>步骤 5。 配置自动向 MediusFlow 预配用户 

此部分逐步介绍了如何将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>若要在 Azure AD 中为 MediusFlow 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用”和“所有应用”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用列表中，选择“MediusFlow”。

    ![应用列表中的“MediusFlow”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入之前检索到的租户 URL 值。 在“密钥令牌”中，输入之前检索到的密钥令牌值。 单击“测试连接”，以确保 Azure AD 可以连接到 MediusFlow。 如果连接失败，请确保 MediusFlow 帐户拥有管理员权限，然后重试。

      ![预配](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件地址”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送一封电子邮件通知”复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选中“将 Azure Active Directory 用户同步到 MediusFlow”。

9. 在“特性映射”部分中，审阅从 Azure AD 同步到 MediusFlow 的用户特性。 选为“匹配”属性的特性是用于匹配 MediusFlow 中用于更新操作的用户帐户。 如果选择更改[匹配目标特性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 MediusFlow API 支持根据相应特性来筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |emails[type eq "work"].value|字符串|
   |name.displayName|字符串|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |externalID|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|


10. 在“映射”部分下，选中“将 Azure Active Directory 组同步到 MediusFlow”。

11. 在“特性映射”部分中，审阅从 Azure AD 同步到 MediusFlow 的组特性。 选为“匹配”属性的特性是用于匹配 MediusFlow 中用于更新操作的组。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalID|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 MediusFlow 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要向 MediusFlow 预配的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会启动“设置”部分的“范围”中定义的所有用户和组的初始同步周期。 初始周期的执行时间比后续周期长，后续周期大约每 40 分钟出现一次，只要 Azure AD 预配服务正在运行。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)，以查看预配周期的状态以及还剩多少完成
3. 如果预配配置似乎处于运行不正常状态，应用将进入隔离状态。 若要详细了解隔离状态，请单击[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
