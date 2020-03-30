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
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205091"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教程：立即配置服务以进行自动用户预配

本教程介绍在 ServiceNow 和 Azure 活动目录 （Azure AD） 中配置自动用户预配所需的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配和取消用户和组服务[Now。](https://www.servicenow.com/) 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持功能
> [!div class="checklist"]
> * 在 ServiceNow 中创建用户
> * 当不再需要访问权限时，删除 ServiceNow 中的用户
> * 使用户属性在 Azure AD 和服务现在之间保持同步
> * 服务现在中的预配组和组成员身份
> * [单次登录](servicenow-tutorial.md)服务现在（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 卡尔加里或更高一些的[ServiceNow 实例](https://www.servicenow.com/)
* 赫尔辛基或更高地区的[ServiceNow Express 实例](https://www.servicenow.com/)
* ServiceNow 中的用户帐户，具有管理员角色

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁将在[预配范围内](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。
3. 确定要在[Azure AD 和服务现在 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)哪些数据。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步骤 2. 将服务现在配置为支持使用 Azure AD 进行预配

1. 标识服务 Now 实例名称。 您可以在用于访问 ServiceNow 的 URL 中找到实例名称。 在下面的示例中，实例名称是 dev35214。

![服务现在实例](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. 获取 ServiceNow 中的管理员的凭据。 导航到 ServiceNow 中的用户配置文件，并验证用户是否具有管理员角色。 

![服务现在管理员角色](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加服务现在

从 Azure AD 应用程序库添加 ServiceNow，开始管理对 ServiceNow 的预配。 如果您以前为 SSO 设置了 ServiceNow，则可以使用相同的应用程序。 但是，建议在最初测试集成时创建单独的应用。 在此处了解有关从库中添加应用程序[的更多详细信息](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁将处于预配范围 

Azure AD 预配服务允许您根据对应用程序的分配以及或基于用户/组的属性来限定谁将预配的范围。 如果选择根据分配将预配到应用的范围，[则可以使用以下步骤将](../manage-apps/assign-user-or-group-access-portal.md)用户和组分配给应用程序。 如果选择仅根据用户或组的属性预配谁的范围，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配给 ServiceNow 时，必须选择**默认访问**以外的角色。 具有默认访问权限角色的用户从预配中排除，并且将在预配日志中标记为无效权限。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 从小开始。 在向所有人推出之前，先与一小部分用户和组进行测试。 将预配范围设置为分配的用户和组时，可以通过为应用分配一个或两个用户或组来控制这种情况。 当作用域设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步骤 5。 配置自动用户预配到服务现在 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>要在 Azure AD 中配置服务现在的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“ServiceNow”****。

    ![应用程序列表中的 ServiceNow 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理员凭据"** 部分下，输入您的 ServiceNow 管理员凭据和用户名。 单击 **"测试连接**"以确保 Azure AD 可以连接到服务现在。 如果连接失败，请确保您的服务 Now 帐户具有管理员权限，然后重试。

    ![预配](./media/servicenow-provisioning-tutorial/provisioning.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到服务现在**。

9. 在**属性映射**部分中查看从 Azure AD 同步到"服务现在"的用户属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的用户帐户以执行更新操作****。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 ServiceNow API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

10. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到服务现在**。

11. 在**属性映射**部分中查看从 Azure AD 同步到"服务现在"的组属性。 选择为 **"匹配属性"** 的属性用于匹配 ServiceNow 中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要启用"立即服务"的 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 ServiceNow 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将开始 **"设置"** 部分中 **"范围**"中定义的所有用户和组的初始同步周期。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务运行，则大约每 40 分钟执行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源监视部署：

1. 使用[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)确定已成功预配或未成功预配哪些用户
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)以查看预配周期的状态以及预配周期与完成有多近
3. 如果预配配置似乎处于不正常状态，则应用程序将进入隔离状态。 在此处了解有关隔离状态的更多[。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>疑难解答指南
* **无效查找参考：** 在 ServiceNow 中预配某些属性（如"部门"和"位置"）时，这些值必须已存在于 ServiceNow 中的引用表中。 例如，在 ServiceNow 中的**插入表名称**表中，您可能有两个位置（西雅图、洛杉矶）和三个部门（销售、财务、市场营销）。 如果您尝试预配其部门为"销售"且位置为"西雅图"的用户，则已成功预配用户。 如果您尝试向用户预配部门"销售"和位置"LA"，则不会预配该用户。 必须将位置 LA 添加到 ServiceNow 中的引用表，或者必须更新 Azure AD 中的用户属性以匹配 ServiceNow 中的格式。 
* **条目联接属性值缺失：** 查看[属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)以标识匹配属性。 此值必须存在于您尝试预配的用户或组中。 
* 查看[ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)以了解任何要求或限制（例如，为用户指定国家/地区代码的格式）
* 某些 ServiceNow 部署需要允许 Azure AD 预配服务的 IP 范围。 Azure AD 预配服务的预留 IP 范围可[在此处](https://www.microsoft.com/download/details.aspx?id=56519)找到"AzureActiveDirectoryDomain 服务"。
* 默认情况下，预配请求发送到 https：//[您的实例名称].服务-now.com/[表名]。 如果需要自定义租户 URL，可以在实例名称字段中提供整个 URL。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
