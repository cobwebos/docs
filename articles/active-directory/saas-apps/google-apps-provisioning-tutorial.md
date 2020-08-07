---
title: 教程：使用 Azure Active Directory 为 G Suite 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 G Suite 及如何取消预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 90e9006a19825059096b81b9c174d16a270775f1
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920307"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教程：为 G Suite 配置自动用户预配

本教程介绍了需要在 G Suite 和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置时，Azure AD 会使用 Azure AD 预配服务自动预配用户和组，并将其预配到[G Suite](https://gsuite.google.com/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

> [!NOTE]
> G Suite 连接器最近于10月2019更新。 对 G Suite 连接器所做的更改包括：
>
> * 添加了对其他 G Suite 用户和组属性的支持。
> * 已更新 G Suite 目标属性名称以匹配[此处](https://developers.google.com/admin-sdk/directory)定义的名称。
> * 已更新默认属性映射。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 G Suite 中创建用户
> * 如果用户不需要访问，请在 G Suite 中删除用户
> * 使用户属性在 Azure AD 和 G Suite 之间保持同步
> * 在 G Suite 中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)到 G Suite (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* [G Suite 租户](https://gsuite.google.com/pricing.html)
* 具有管理员权限的 G Suite 上的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要[在 Azure AD 和 G Suite 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 G Suite 以支持 Azure AD 的预配

在将 G Suite 配置为使用 Azure AD 进行自动用户预配之前，需要在 G Suite 上启用 SCIM 预配。

1. 用管理员帐户登录到[G Suite 管理控制台](https://admin.google.com/)，然后选择 "**安全性**"。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”**** 菜单下。

    ![G Suite 安全性](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. 在“安全”页上，选择“API 参考”********。

    ![G Suite API](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. 选择 **“启用 API 访问”**。

    ![已启用 G Suite API](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > 对于要预配到 G Suite 的每个用户，Azure AD 中的用户名**必须**绑定到自定义域。 例如，G Suite 不会接受 bob@contoso.onmicrosoft.com 之类的用户名， 但会接受 bob@contoso.com。 可以按照[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的说明更改现有用户的域。

4. 使用 Azure AD 添加并验证所需的自定义域后，必须使用 G Suite 再次验证它们。 若要验证 G Suite 中的域，请参阅以下步骤：

    a. 在[G Suite 管理员控制台](https://admin.google.com/)中，选择 "**域**"。

    ![G Suite 域](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. 选择“添加域或域别名”****。

    ![G Suite 添加域](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. 选择“添加另一个域”，然后键入要添加的域名****。

    ![G Suite 添加另一个](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. 选择“继续验证域所有权”****。 然后按步骤验证所拥有的域名。 有关如何通过 Google 验证你的域的全面说明，请参阅[验证站点所有权](https://support.google.com/webmasters/answer/35179)。

    e. 对要添加到 G Suite 的任何其他域重复上述步骤。

5. 接下来，确定要用于在 G Suite 中管理用户设置的管理员帐户。 导航到 "**管理员角色**"。

    ![G Suite 管理员](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. 对于该帐户的**管理员角色**，请编辑该角色的**权限**。 请确保启用该帐户的所有“管理员 API 权限”，使其可用于预配****。

    ![G Suite 管理员权限](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 G Suite

从 Azure AD 应用程序库添加 G Suite，开始管理到 G Suite 的预配。 如果以前为 SSO 设置了 G Suite，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 G Suite 时，必须选择 "**默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>步骤 5。 配置 G Suite 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

> [!NOTE]
> 若要详细了解 G Suite 的目录 API 终结点，请参阅[目录 api](https://developers.google.com/admin-sdk/directory)。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>若要在 Azure AD 中配置 G Suite 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。 用户需要登录到 portal.azure.com，并且将不能使用 aad.portal.azure.com

    ![“企业应用程序”边栏选项卡](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![“所有应用程序”边栏选项卡](./media/google-apps-provisioning-tutorial/all-applications.png)

2. 在应用程序列表中，选择“G Suite”****。

    ![应用程序列表中的 G Suite 链接](common/all-applications.png)

3. 选择“预配”选项卡。单击“入门”。

    ![“预配”选项卡](common/provisioning.png)

      ![“开始”边栏选项卡](./media/google-apps-provisioning-tutorial/get-started.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分中，单击 "**授权**"。 你将在新的浏览器窗口中重定向到 Google 授权对话框。

      ![G Suite 授权](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. 确认你要为你的 G Suite 租户授予 Azure AD 的权限。 选择“接受”。

     ![G Suite 租户身份验证](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. 在 Azure 门户中，单击 "**测试连接**" 以确保 Azure AD 可以连接到 G Suite。 如果连接失败，请确保 G Suite 帐户具有管理员权限，然后重试。 然后再次重试“授权”步骤****。

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“预配 Azure Active Directory 用户” 。

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 G Suite 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 G Suite 中的用户帐户以执行更新操作。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 G Suite API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |primaryEmail|字符串|
   |关系.[type eq "manager"]。值|字符串|
   |name.familyName|字符串|
   |name.givenName|字符串|
   |已挂起|字符串|
   |externalIds.[type eq "custom"]。值|字符串|
   |externalIds.[type eq "组织"]。值|字符串|
   |地址.[type eq "work"]。国家/地区|字符串|
   |地址.[type eq "work"]. streetAddress|字符串|
   |地址.[type eq "work"]。区域|字符串|
   |地址.[type eq "work"]。位置|字符串|
   |地址.[type eq "work"]. 邮政编码|字符串|
   |封.[type eq "work"]. address|字符串|
   |组织.[type eq "work"]. 部门|字符串|
   |组织.[type eq "work"]。标题|字符串|
   |phoneNumbers.[type eq "work"]。值|字符串|
   |phoneNumbers.[type eq "mobile"]。值|字符串|
   |phoneNumbers.[type eq "work_fax"]。值|字符串|
   |封.[type eq "work"]. address|字符串|
   |组织.[type eq "work"]. 部门|字符串|
   |组织.[type eq "work"]。标题|字符串|
   |phoneNumbers.[type eq "work"]。值|字符串|
   |phoneNumbers.[type eq "mobile"]。值|字符串|
   |phoneNumbers.[type eq "work_fax"]。值|字符串|
   |地址.[type eq "home"]。国家/地区|字符串|
   |地址.[type eq "home"]。格式|字符串|
   |地址.[类型 eq "home"]。位置|字符串|
   |地址.[type eq "home"]. 邮政编码|字符串|
   |地址.[type eq "home"]. region|字符串|
   |地址.[type eq "home"]. streetAddress|字符串|
   |地址.[type eq "other"]。国家/地区|字符串|
   |地址.[type eq "other"]。格式|字符串|
   |地址.[type eq "other"]。位置|字符串|
   |地址.[type eq "other"]。邮政编码|字符串|
   |地址.[type eq "other"]。区域|字符串|
   |地址.[type eq "other"]. streetAddress|字符串|
   |地址.[type eq "work"]。格式|字符串|
   |changePasswordAtNextLogin|字符串|
   |封.[type eq "home"]. address|字符串|
   |封.[type eq "other"]. address|字符串|
   |externalIds.[type eq "account"]。值|字符串|
   |externalIds.[type eq "custom"]. customType|字符串|
   |externalIds.[type eq "customer"]。值|字符串|
   |externalIds.[type eq "login_id"]。值|字符串|
   |externalIds.[键入 eq "network"]。值|字符串|
   |性别。类型|字符串|
   |GeneratedImmutableId|字符串|
   |标识符|字符串|
   |ims.[type eq "home"]。协议|字符串|
   |ims.[type eq "other"]。协议|字符串|
   |ims.[type eq "work"]。协议|字符串|
   |includeInGlobalAddressList|字符串|
   |ipWhitelisted|字符串|
   |组织.[type eq "school"]. costCenter|字符串|
   |组织.[type eq "school"]. 部门|字符串|
   |组织.[type eq "school"]。域|字符串|
   |组织.[type eq "school"]. fullTimeEquivalent|字符串|
   |组织.[type eq "school"]。位置|字符串|
   |组织.[type eq "school"]。名称|字符串|
   |组织.[type eq "school"]。符号|字符串|
   |组织.[type eq "school"]。标题|字符串|
   |组织.[type eq "work"]. costCenter|字符串|
   |组织.[type eq "work"]。域|字符串|
   |组织.[type eq "work"]. fullTimeEquivalent|字符串|
   |组织.[type eq "work"]。位置|字符串|
   |组织.[type eq "work"]。名称|字符串|
   |组织.[type eq "work"]。符号|字符串|
   |OrgUnitPath|字符串|
   |phoneNumbers.[type eq "home"]。值|字符串|
   |phoneNumbers.[type eq "other"]。值|字符串|
   |web.[type eq "home"]。值|字符串|
   |web.[type eq "other"]。值|字符串|
   |web.[type eq "work"]。值|字符串|
   

10. 在 "**映射**" 部分下，选择 "**设置 Azure Active Directory 组**"。

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 G Suite 的组属性。 选为 "**匹配**" 属性的属性用于匹配 G Suite 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |电子邮件|字符串|
      |成员|String|
      |name|字符串|
      |description|字符串|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 G Suite 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 G Suite 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

> [!NOTE]
> 如果用户已有使用 Azure AD 用户的电子邮件地址的现有个人/使用者帐户，则可能会在执行目录同步之前使用 Google 传输工具解决某个问题。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
