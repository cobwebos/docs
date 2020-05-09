---
title: 教程：使用 Azure Active Directory 为 Samanage 配置自动用户预配 | Microsoft Docs
description: 了解如何自动将用户 Azure AD 帐户预配到 Samanage 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 182d314b24ce082d996cb692e2a7bb35265abcfe
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628025"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>教程：为 Samanage 配置自动用户预配
本教程介绍了需要在 Samanage 和 Azure Active Directory （Azure AD）中执行的步骤，以配置自动用户预配。 配置后，Azure AD 使用 Azure AD 预配服务自动设置用户和组并取消其预配到[Samanage](https://www.samanage.com/pricing/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="migrate-to-the-new-samange-application"></a>迁移到新的 Samange 应用程序

如果已有与 Samanage 的集成，请参阅以下部分，了解即将发生的更改。 如果是第一次设置 Samanage，可以跳过本部分并转到**支持的功能**。

#### <a name="whats-changing"></a>有什么变化？
* Azure AD 端的更改：在 Samange 中预配用户的授权方法一直以来都是**基本身份验证**。不久，你会看到授权方法已更改为**长生存期机密令牌**。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>将现有自定义集成迁移到新应用程序需要执行哪些操作？
如果现有的 Samanage 集成具有有效的管理员凭据，则**无需执行任何操作**。 我们会自动将客户迁移到新应用程序。 此过程完全在幕后完成。 如果现有凭据过期，或者如果需要再次授权访问应用程序，则需要生成一个长期机密令牌。 若要生成新令牌，请参阅本文的步骤2。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判断应用程序是否已迁移？ 
迁移你的应用程序后，在 "**管理员凭据**" 部分中，"**管理员用户名**" 和 "**管理员密码**" 字段将替换为 "单个**机密令牌**" 字段。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Samanage 中创建用户
> * 当用户不再需要访问权限时，删除 Samanage 中的用户
> * 使用户属性在 Azure AD 和 Samanage 之间保持同步
> * 在 Samanage 中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial)到 Samanage （推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中的一个用户帐户，有权配置预配（例如，应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）的[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。 
* 具有专业包的[Samanage 租户](https://www.samanage.com/pricing/)。
* Samanage 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定将处于[预配范围内的](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)用户。
3. 确定要[在 Azure AD 与 Samanage 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Samanage 以支持 Azure AD 的预配

若要生成用于身份验证的机密令牌，请参阅[此](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)。

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Samanage

从 Azure AD 应用程序库中添加 Samanage，开始管理预配到 Samanage。 如果以前为 SSO 设置了 Samanage，则可以使用相同的应用程序。 但建议您在最初测试集成时创建一个单独的应用程序。 在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)了解有关从库中添加应用程序的详细信息。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义将在设置范围内的人员 

Azure AD 预配服务允许你确定将根据分配给应用程序的人员，或基于用户/组属性进行预配的用户的范围。 如果选择将根据分配预配到你的应用的用户的范围，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性设置的作用域，则可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 Samanage 时，必须选择 "**默认" 访问权限**以外的其他角色。 具有默认访问角色的用户将从预配中排除，并在预配日志中被标记为不有效。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)来添加其他角色。 

* 从小开始。 在向所有人推出之前，请使用少量的用户和组进行测试。 如果设置的作用域设置为 "分配的用户和组"，则可以通过将一个或两个用户或组分配到应用来对此进行控制。 当作用域设置为 "所有用户和组" 时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>步骤 5。 配置 Samanage 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>若要在 Azure AD 中为 Samanage 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Samanage”****。

    ![应用程序列表中的 Samanage 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将**预配模式**设置为 "**自动**"。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分中`https://api.samanage.com` ，输入 "**租户 URL**"。  输入先前在 "**机密令牌**" 中检索到的机密令牌值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Samanage。 如果连接失败，请确保 Samanage 帐户具有管理员权限，然后重试

    ![预配](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在 "**通知电子邮件**" 字段中，输入应接收预配错误通知的人员或组的电子邮件地址，并选中 "**发生故障时发送电子邮件通知**" 复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”  。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Samanage**"。

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Samanage 的用户属性。 选为“匹配”属性的特性用于匹配 Samanage 中的用户帐户以执行更新操作****。 如果选择更改[匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 Samanage API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改****。

      ![Samange 用户映射](./media/samanage-provisioning-tutorial/user-attributes.png)

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Samanage”********。

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Samanage 的组属性。 选为“匹配”**** 属性的特性用于匹配 Samanage 中的组以执行更新操作。 选择“保存”按钮以提交任何更改****。

      ![Samange 组映射](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Samanage 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Samanage 的用户和/或组********。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将启动 "**设置**" 部分的 "**范围**" 中定义的所有用户和组的初始同步循环。 初始周期比后续循环长，只要 Azure AD 预配服务正在运行，就大约每40分钟执行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6。 监视部署
配置预配后，请使用以下资源来监视部署：

1. 使用[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已成功设置或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)，查看设置周期的状态以及完成操作的方式
3. 如果预配配置似乎处于不正常状态，则应用程序将进入隔离区。 [在此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="connector-limitations"></a>连接器限制

如果选择 "**同步所有用户和组**" 选项并为 "Samanage **roles** " 属性配置值，则必须用以下格式表示**默认值 "如果为 null （可选）** " 框下的值：

- {"displayName"： "role"}，其中 role 是所需的默认值。

## <a name="change-log"></a>更改日志

* 04/22/2020-更新了从基本身份验证到长生存期机密令牌的授权方法。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
