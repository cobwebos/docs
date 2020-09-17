---
title: 教程：配置 SolarWinds Service 书桌 (以前 Samanage 的) ，Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何自动预配和取消预配用户帐户，使其从 Azure AD 到 SolarWinds Service 书桌 (之前 Samanage) 。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707260"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>教程：配置 SolarWinds Service 书桌 (以前 Samanage 的) 以实现自动用户预配

本教程介绍了在 SolarWinds Service 书桌 (之前 Samanage) 和 Azure Active Directory (Azure AD) 配置自动用户预配时需要执行的步骤。 配置后，Azure AD 会自动将用户和组预配和取消预配到使用 Azure AD 预配服务的 [SolarWinds](https://www.samanage.com/pricing/) 服务台。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>迁移到新的 SolarWinds Service 书桌应用程序

如果已与 SolarWinds 服务台集成，请参阅以下部分，了解即将发生的更改。 如果你是第一次设置 SolarWinds Service 书桌，则可以跳过本部分并转到 **支持的功能**。

#### <a name="whats-changing"></a>有什么变化？

* Azure AD 端的更改：在 Samange 中预配用户的授权方法一直以来都是 **基本身份验证**。不久，你会看到授权方法已更改为 **长生存期机密令牌**。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>将现有自定义集成迁移到新应用程序需要执行哪些操作？

如果现有的 SolarWinds 服务台集成具有有效的管理员凭据，则 **无需执行任何操作**。 我们会自动将客户迁移到新应用程序。 此过程完全在幕后完成。 如果现有凭据过期，或者如果需要再次授权访问应用程序，则需要生成一个长期机密令牌。 若要生成新令牌，请参阅本文的步骤2。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判断应用程序是否已迁移？ 

迁移你的应用程序后，在 " **管理员凭据** " 部分中，" **管理员用户名** " 和 " **管理员密码** " 字段将替换为 "单个 **机密令牌** " 字段。

## <a name="capabilities-supported"></a>支持的功能

> [!div class="checklist"]
> * 在 SolarWinds 服务台中创建用户
> * 如果用户不需要访问，请在 SolarWinds 服务台中删除用户
> * 使用户属性在 Azure AD 和 SolarWinds 服务台之间保持同步
> * 在 SolarWinds 服务台中预配组和组成员身份
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) 到 SolarWinds 服务台 (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中[有权](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 具有专业包的 [SolarWinds Service 书桌租户](https://www.samanage.com/pricing/) 。
* SolarWinds 服务台中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 和 SolarWinds 服务台之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 SolarWinds 服务台以支持 Azure AD 的预配

若要生成用于身份验证的机密令牌，请参阅 [API 集成的教程令牌身份验证](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)。

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 SolarWinds 服务台

从 Azure AD 应用程序库中添加 SolarWinds 服务台，开始管理 SolarWinds 服务台的预配。 如果以前为 SSO 设置了 SolarWinds 服务台，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户和组分配到 SolarWinds 服务台时，必须选择 " **默认" 访问权限**以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>步骤 5。 配置 SolarWinds 服务台的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>若要在 Azure AD 中配置 SolarWinds 服务台的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **SolarWinds**服务台"。

3. 选择“预配”选项卡。

    ![显示选定 "预配" 选项卡的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![显示设置为自动的设置模式的屏幕截图。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入 " `https://api.samanage.com` **租户 URL**"。  在“机密令牌”中，输入之前检索到的机密令牌值。 选择 " **测试连接** " 以确保 Azure AD 可以连接到 SolarWinds 服务台。 如果连接失败，请确保 SolarWinds 服务台帐户具有管理员权限，然后重试。

    ![显示选定的 "测试连接" 按钮的屏幕截图。](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 SolarWinds**服务台"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 SolarWinds 服务台的用户属性。 选为 " **匹配** " 属性的属性用于匹配 SolarWinds 服务台中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 SOLARWINDS 服务台 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

      ![Samange 用户映射](./media/samanage-provisioning-tutorial/user-attributes.png)

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步到 SolarWinds**服务台"。

11. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 SolarWinds 服务台的组属性。 选为 " **匹配** " 属性的属性用于匹配 SolarWinds 服务台中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      ![Samange 组映射](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 SolarWinds 服务台启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 SolarWinds 服务台的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 准备好进行预配时，请选择 " **保存**"。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="connector-limitations"></a>连接器限制

如果选择 " **同步所有用户和组** " 选项并为 "SolarWinds 服务台 **角色** " 属性配置值，则默认值下的值（ **如果为 null (为可选）) ** 框必须采用以下格式表示：

- {"displayName"： "role"}，其中 role 是所需的默认值。

## <a name="change-log"></a>更改日志

* 09/14/2020-将两个 SaaS 教程中的公司名称从 Samanage 更改为 SolarWinds 服务中心 (以前每个 Samanage) https://github.com/ravitmorales 。
* 04/22/2020-更新了从基本身份验证到长生存期机密令牌的授权方法。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
