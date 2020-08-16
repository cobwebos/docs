---
title: 教程：在 Azure Active Directory 中配置缩放以实现自动用户预配 |Microsoft Docs
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 52700df5c2977f33c988fcb34cdc3d67844d1d49
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257577"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>教程：为自动用户预配配置缩放

本教程介绍了在缩放和 Azure Active Directory (Azure AD) 配置自动用户预配时需要执行的步骤。 配置时，Azure AD 会自动预配和取消预配用户和组，以使用 Azure AD 预配服务进行 [缩放](https://zoom.us/pricing/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在缩放中创建用户
> * 如果用户不再需要访问，请在 "缩放" 中删除用户
> * 使用户属性在 Azure AD 和缩放之间保持同步
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) 到缩放 (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* [缩放租户](https://zoom.us/pricing)。
* 使用管理员权限缩放的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 和缩放之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置缩放以支持设置 Azure AD

1. 登录到 [缩放管理控制台](https://zoom.us/signin)。 导航到左侧导航窗格中的 " **高级 > 应用商店** "。

    ![缩放集成](media/zoom-provisioning-tutorial/zoom01.png)

2. 在页面的右上角导航到 " **管理** "。 

    ![缩放安装](media/zoom-provisioning-tutorial/zoom02.png)

3. 导航到创建的 Azure AD 应用。 
    
    ![缩放应用](media/zoom-provisioning-tutorial/zoom03.png)

4. 在左侧导航窗格中选择 " **应用凭据** "。

    ![缩放应用](media/zoom-provisioning-tutorial/zoom04.png)

5. 复制并保存 **JWT 标记**。 此值将输入到 Azure 门户中缩放应用程序 "预配" 选项卡的 " **机密令牌** " 字段中。 如果需要新的不过期令牌，将需要重新配置过期时间，这将自动生成新令牌。 

    ![缩放安装](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加缩放

添加 Azure AD 应用程序库中的 "缩放"，开始管理预配以进行缩放。 如果以前为 SSO 设置了缩放，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 "缩放" 时，必须选择 " **默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>步骤 5。 配置自动用户预配以进行缩放 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>若要配置用于放大 Azure AD 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zoom”****。

    ![应用程序列表中的 Zoom 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入 " `https://api.zoom.us/scim` **租户 URL**"。 输入先前在 "**机密令牌**" 中检索到的**JWT 令牌**值。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 "缩放"。 如果连接失败，请确保缩放帐户具有管理员权限，然后重试。

    ![缩放设置](./media/zoom-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **同步 Azure Active Directory 用户进行缩放**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 "缩放" 的用户属性。 选为 " **匹配** " 属性的属性用于匹配缩放中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保缩放 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |电子邮件 [类型 eq "work"]|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为缩放启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要进行缩放的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。  

## <a name="connector-limitations"></a>连接器限制
* 缩放最多允许当今最多9999个基本用户。

## <a name="change-log"></a>更改日志
* 05/14/2020-支持为电子邮件添加的更新操作 [类型 eq "work"] 属性。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
