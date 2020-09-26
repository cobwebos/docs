---
title: 教程：通过 Azure Active Directory 为 Apple School Manager 配置自动用户预配 |Microsoft Docs
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: 221039ad1dc30e645d98ba6dd7e79c7b81ba3430
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313053"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>教程：为 Apple School Manager 配置自动用户预配



本教程介绍了需要在 Apple School Manager 和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置后，Azure AD 使用 Azure AD 预配服务自动预配用户并将其预配到 [Apple School Manager](https://school.apple.com/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Apple 学校管理器中创建用户
> * 如果用户不需要访问，请在 Apple School Manager 中删除用户
> * 使特定用户属性在 Azure AD 和 Apple School Manager 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 有 [权](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 配置预配 (的用户帐户，例如，应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员) 。 
* 具有 "管理员"、"站点经理" 或 "人员经理" 角色的 Apple School Manager 帐户。

> [!NOTE]
> 令牌传输到 Azure AD 并建立成功的连接必须在4个日历天内完成，否则必须重新启动此过程。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定要 [在 Azure AD 和 Apple School Manager 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>步骤 2. 将 Apple School Manager 配置为支持预配 Azure AD

1. 在 Apple 学校管理器中，使用角色为 "管理员"、"站点经理" 或 "人员经理" 的帐户进行登录。
2. 单击侧栏底部的 "设置"，单击 "组织设置" 下方的 "数据源"，然后单击 "连接到数据源"。
3. 单击 "SCIM" 旁边的 "连接"，仔细阅读警告，单击 "复制"，然后单击 "关闭"。
[连接到 SCIM 窗口，该窗口在其下提供标记和复制按钮。]将此窗口保持为打开状态，将租户 URL 从 Apple Business Manager 复制到 Azure AD，这是： https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> 不应与 Azure AD 管理员之外的任何人共享机密令牌。

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Apple School Manager

从 "Azure AD" 应用程序库中添加 Apple School Manager，开始管理预配到 Apple School Manager。 如果你以前为 SSO 安装了 Apple School Manager，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)详细了解如何从库中添加应用程序。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户分配到 Apple School Manager 时，必须选择 " **默认" 访问权限**以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>步骤 5。 为 Apple School Manager 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **Apple School Manager**"。

    ![应用程序列表中的 Apple School Manager](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![自动设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下，输入从**租户 URL**中的 Apple School Manager 中检索的**SCIM 2.0 基 URL 和访问令牌**值，并分别输入**密钥**。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 Apple School Manager。 如果连接失败，请确保你的 Apple School Manager 帐户具有管理员权限，然后重试。

    ![令牌](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>如果连接成功，则 Apple School Manager 会将 SCIM 连接显示为 "活动"。 Apple School Manager 可能需要长达60秒才能反映最新的连接状态。

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 Apple School Manager**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Apple School Manager 的用户属性。 选为 " **匹配** " 属性的属性用于匹配 Apple School Manager 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |userName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.givenName|字符串|
   |externalId|String|
   |区域设置|字符串|
   |timezone|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Apple School Manager 启用 Azure AD 预配服务，请在 "设置" 部分中将 " **预配状态** " 更改为 **"打开** "。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Apple School Manager 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署

配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [查看 Apple School Manager 的 SCIM 要求](URL=https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Apple School Manager 中使用人员 ID 的方式](URL=https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [使用 SCIM 将用户导入 Apple School Manager](URL=https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [解决 Apple School Manager 中的 SCIM 用户帐户冲突](URL=https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [删除 Apple School Manager 中显示 Azure AD 帐户](URL=https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [在 Apple School Manager 中查看 SCIM 活动](URL=https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [在 Apple 学校管理器中管理现有的 SCIM 令牌和连接](URL=https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [断开 Apple School Manager 中的 SCIM 连接](URL=https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [在 Apple 学校管理器中管理现有的 SCIM 令牌和连接](URL=https://support.apple.com/guide/apple-school-manager/apdc9a8236e9)
* [Apple School Manager 中的 SCIM 连接疑难解答](URL=https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
