---
title: 教程：配置 Coda 以实现 Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: ce70a4df50be9004182e80711de449bab146a800
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360899"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Coda

本教程介绍了在 Coda 和 Azure Active Directory (Azure AD) 配置自动用户预配时需要执行的步骤。 配置后，Azure AD 使用 Azure AD 预配服务自动预配用户并将其预配到 [Coda](https://coda.io/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Coda 中创建用户
> * 如果用户不再需要访问，请将其删除
> * 使用户属性在 Azure AD 和 Coda 之间保持同步
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) 到 Coda (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* 具有配置预配[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [Coda 企业](https://help.coda.io/en/articles/3520174-getting-started-with-sso)管理员帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 和 Coda 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Coda 以支持 Azure AD 的预配

1. 通过选择 "组织设置" 下的 "..."，打开组织管理控制台菜单下。

    ![Coda 企业组织 SCIM 设置](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. 请确保已启用 SCIM 预配。
3. 请注意 SCIM 基 URL 和 SCIM 持有者令牌。 如果没有持有者令牌，请单击 "生成新令牌"。

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Coda

从 Azure AD 应用程序库添加 Coda，开始管理到 Coda 的预配。 如果以前为 SSO 设置了 Coda，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中

Azure AD 预配服务允许你确定将根据分配给应用程序的人员，或者根据用户的属性设置的用户的身份。 如果选择将根据分配预配到你的应用的用户的范围，则可以使用以下 [步骤](../manage-apps/assign-user-or-group-access-portal.md) 将用户分配到应用程序。 如果选择仅根据用户的属性设置的作用域，则可以使用 [此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。

* 将用户分配到 Coda 时，必须选择 " **默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。

* 先小部分测试。 使用少量用户进行测试，然后再向所有人推出。 如果设置的作用域设置为 "分配的用户"，则可以通过将一个或两个用户分配到应用来对此进行控制。 如果作用域设置为 "所有用户"，则可以指定 [基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>步骤 5。 配置 Coda 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务，以便基于 Azure AD 中的用户分配在 TestApp 中创建、更新和禁用用户的步骤。

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>若要为 Azure AD 中的 Coda 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“Coda”。****

    ![应用程序列表中的 Coda 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![带有称为 "预配" 选项的 "管理" 选项的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![具有 "自动" 选项的 "预配模式" 下拉列表屏幕截图。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分下，输入你在步骤2中先前检索到的 CODA 租户 URL 和机密令牌。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 Coda。 如果连接失败，请确保 Coda 帐户具有管理员权限，然后重试。

    ![屏幕截图显示 "管理员凭据" 对话框，你可以在其中输入租户 U R L 和机密令牌。](./media/coda-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 Coda**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Coda 的用户属性。 选为 " **匹配** " 属性的属性用于匹配 Coda 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 Coda API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Coda 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Coda 的用户。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将启动在 "**设置**" 部分的 "**范围**" 中定义的所有用户的初始同步循环。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

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
