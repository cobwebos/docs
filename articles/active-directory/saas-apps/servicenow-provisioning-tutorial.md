---
title: 教程：使用 Azure Active Directory 为 ServiceNow 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 ServiceNow 及如何取消预配。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: da62efff5db5c71b087657b0eec93f8dd4702665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84751504"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教程：为 ServiceNow 配置自动用户预配

本教程介绍了在 ServiceNow 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [ServiceNow](https://www.servicenow.com/) 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 ServiceNow 中创建用户
> * 在用户不再有访问需求的情况下，在 ServiceNow 中删除用户
> * 使用户属性在 Azure AD 和 ServiceNow 之间保持同步
> * 在 ServiceNow 中预配组和组成员身份
> * [单一登录](servicenow-tutorial.md)到 ServiceNow（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* Calgary 或更高版本的 [ServiceNow 实例](https://www.servicenow.com/)
* Helsinki 或更高版本的 [ServiceNow Express 实例](https://www.servicenow.com/)
* ServiceNow 中具有管理员角色的用户帐户

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定[在 Azure AD 与 ServiceNow 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 ServiceNow 以支持通过 Azure AD 进行预配

1. 确定 ServiceNow 实例名称。 可以在用于访问 ServiceNow 的 URL 中找到实例名称。 在下面的示例中，实例名称为 dev35214。

   ![ServiceNow 实例](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. 获取 ServiceNow 中管理员的凭据。 导航到 ServiceNow 中的用户配置文件，并验证该用户是否具有管理员角色。 

   ![ServiceNow 管理员角色](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. 检查以确保在 ServiceNow 中**禁用**了以下设置：

   1. 选择 "**系统安全**  >  **高级安全设置**"  >  **要求对传入架构请求进行基本身份验证**。
   2. 选择 "**系统属性**"  >  **Web 服务**  >  **需要对传入 SOAP 请求进行基本授权**。
     
   > [!IMPORTANT]
   > 如果*启用*了这些设置，则设置引擎将无法与 ServiceNow 通信。

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 ServiceNow

从 Azure AD 应用程序库添加 ServiceNow，开始管理到 ServiceNow 的预配。 如果以前为 SSO 设置过 ServiceNow，则可以使用同一应用程序。 但建议在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来查看要对谁进行预配，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 ServiceNow 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步骤 5。 对 ServiceNow 配置自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>若要在 Azure AD 中为 ServiceNow 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“ServiceNow”。

    ![应用程序列表中的 ServiceNow 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，输入 ServiceNow 管理员凭据和用户名。 单击“测试连接”以确保 Azure AD 可以连接到 ServiceNow。 如果连接失败，请确保 ServiceNow 帐户具有管理员权限，然后重试。

    ![预配](./media/servicenow-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 ServiceNow” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 ServiceNow 的用户属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 ServiceNow API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 ServiceNow” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 ServiceNow 的组属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 ServiceNow 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 ServiceNow 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)。  

## <a name="troubleshooting-tips"></a>故障排查提示
* InvalidLookupReference：在 ServiceNow 中预配某些属性（如 Department 和 Location）时，这些值必须已存在于 ServiceNow 的引用表中。 例如，在 ServiceNow 的“插入表名称”表中，可能有两个地点（西雅图、洛杉矶）和三个部门（销售、财务、市场）。 如果尝试预配部门为“销售”以及地点为“西雅图”的用户，则该用户将预配成功。 如果尝试预配部门为“销售”以及地点为“洛杉矶”的用户，则该用户将预配失败。 必须将位置洛杉矶添加到 ServiceNow 的引用表中，或者必须更新 Azure AD 中的用户属性才能匹配 ServiceNow 中的格式。 
* EntryJoiningPropertyValueIsMissing：查看[属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)以识别匹配的属性。 该值必须存在于要预配的用户或组上。 
* 查看 [ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) 以了解任何要求或限制（例如，为用户指定国家/地区代码的格式）
* 默认情况下，预配请求将发送到 https://{你的实例名称}.service-now.com/{表名称}。 如果需要一个自定义租户 URL，可以在实例名称字段中提供整个 URL。
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   此错误表示与 ServiceNow 实例通信时出现问题。 仔细检查以确保在 ServiceNow 中*禁用*了以下设置：
   
   1. 选择 "**系统安全**  >  **高级安全设置**"  >  **要求对传入架构请求进行基本身份验证**。
   2. 选择 "**系统属性**"  >  **Web 服务**  >  **需要对传入 SOAP 请求进行基本授权**。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
