---
title: 教程：配置 Oracle 云基础结构控制台，以便使用 Azure 活动目录进行自动用户预配 |微软文档
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户到 Oracle 云基础结构控制台。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378946"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>教程：配置 Oracle 云基础架构控制台以进行自动用户预配

本教程介绍在 Oracle 云基础结构控制台和 Azure 活动目录 （Azure AD） 中配置自动用户预配所需的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配和取消向[Oracle 云基础结构控制台](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton)预配和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 在 Oracle 云基础架构控制台中创建用户
> * 在 Oracle 云基础架构控制台中删除用户，当他们不再需要访问权限时
> * 使用户属性在 Azure AD 和 Oracle 云基础结构控制台之间保持同步
> * Oracle 云基础架构控制台中的预配组和组成员身份
> * [单次登录](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)Oracle 云基础架构控制台（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* Oracle 云基础架构控制[租户](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)。
* 具有管理员权限的 Oracle 云基础结构控制中的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 确定[要在 Azure AD 和 Oracle 云基础结构控制台之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Oracle 云基础架构控制台以支持使用 Azure AD 进行预配

1. 登录到 Oracle 云基础架构控制台的管理门户。 在屏幕的左上角导航到**身份>联合**。

    ![甲骨文管理员](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. 单击 Oracle 身份云服务控制台旁边的页面上显示的 URL。

    ![甲骨文 URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. 单击"**添加标识提供程序**"以创建新的标识提供程序。 保存 IdP ID 以用作租户 URL 的一部分。单击 **"应用程序"** 选项卡旁边的加号图标以创建 OAuth 客户端并授予 IDCS 标识域管理员 AppRole。

    ![甲骨文云图标](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 按照下面的屏幕截图来配置应用程序。 完成配置后，单击"**保存**"。

    ![甲骨文配置](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle 令牌颁发策略](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. 在应用程序的配置选项卡下展开 **"常规信息"** 选项以检索客户端 ID 和客户端机密。

    ![Oracle 令牌生成](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. 要生成机密令牌 Base64，在**客户端 ID：客户端密钥**的格式中对客户端 ID 和客户端密钥进行编码。 保存密钥令牌。 此值将在 Azure 门户中的 Oracle 云基础结构控制台应用程序的预配选项卡中的 **"机密令牌"** 字段中输入。

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Oracle 云基础结构控制台

从 Azure AD 应用程序库添加 Oracle 云基础结构控制台，以开始管理对 Oracle 云基础结构控制台的预配。 如果您以前为 SSO 设置了 Oracle 云基础架构控制台，则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配给 Oracle 云基础结构控制台时，必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>步骤 5。 配置到 Oracle 云基础架构控制台的自动用户预配 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>要在 Azure AD 中配置 Oracle 云基础结构控制台的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Oracle Cloud Infrastructure Console”****。

    ![应用程序列表中的 Oracle 云基础结构控制台链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下，以格式`https://<IdP ID>.identity.oraclecloud.com/admin/v1`输入**租户 URL。** 例如，`https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`。 输入在**秘密令牌**中检索到的秘密令牌值。 单击 **"测试连接**"以确保 Azure AD 可以连接到 Oracle 云基础结构控制台。 如果连接失败，请确保您的 Oracle 云基础结构控制台帐户具有管理员权限，然后重试。

    ![预配](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。 

8. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到 Oracle 云基础结构控制台**。

9. 在**属性映射**部分中查看从 Azure AD 同步到 Oracle 云基础结构控制台的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Oracle 云基础结构控制台中的用户帐户以进行更新操作。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Oracle 云基础结构控制台 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

      |特性|类型|
      |---|---|
      |displayName|字符串|
      |userName|字符串|
      |活动|Boolean|
      |title|字符串|
      |emails[type eq "work"].value|字符串|
      |preferredLanguage|字符串|
      |name.givenName|字符串|
      |name.familyName|字符串|
      |地址[类型 eq"工作"]格式化|字符串|
      |地址_类型 eq"工作"\局部性|字符串|
      |地址_类型 eq"工作"\区域|字符串|
      |addresses[type eq "work"].postalCode|字符串|
      |地址[类型 eq"工作"\国家/地区|字符串|
      |addresses[type eq "work"].streetAddress|字符串|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：员工编号|字符串|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|字符串|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：成本中心|字符串|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：部门|字符串|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：经理|参考|
      |urn：ietf：参数：scim：架构：扩展：企业：2.0：用户：组织|字符串|
      |urn：ietf：参数：scim：schema：oracle：idc：扩展：用户：用户：旁路通知|Boolean|
      |urn：ietf：参数：scim：schema：oracle：idc：扩展：用户：用户：是联盟用户|Boolean|

10. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到 Oracle 云基础结构控制台**。

11. 在**属性映射**部分中查看从 Azure AD 同步到 Oracle 云基础结构控制台的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Oracle 云基础结构控制台中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

      |特性|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Oracle 云基础结构控制台启用 Azure AD 预配服务，请将"**设置"** 部分中的 **"预配状态**更改为**打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Oracle 云基础结构控制台的用户和/或组。

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
