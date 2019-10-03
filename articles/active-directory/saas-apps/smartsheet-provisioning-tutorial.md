---
title: 教程：配置自动用户预配 Azure Active Directory 与 Smartsheet |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配到 Smartsheet 的用户帐户。
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670935"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教程：配置自动用户预配的 Smartsheet

本教程的目的是为了演示在 Smartsheet 和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消其预配的用户和/或组到 Smartsheet 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[补充使用条款的 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>系统必备

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Smartsheet 租户](https://www.smartsheet.com/pricing)
* 在 Smartsheet 企业版或企业高级支持计划中具有系统管理员权限的用户帐户。

## <a name="assign-users-to-smartsheet"></a>将用户分配到 Smartsheet

Azure Active Directory 使用称为的概念*分配*来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步仅用户和/或组已分配给 Azure AD 中的应用程序。

在配置和启用自动用户预配前, 应确定哪些用户和/或 Azure AD 中的组需要到 Smartsheet 的访问权限。 确定后，您可以将这些用户和/或组到 Smartsheet 分配按照此处的说明：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>将用户分配到 Smartsheet 的重要提示

* 建议将单个 Azure AD 用户分配到 Smartsheet 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 当将用户分配到 Smartsheet，您必须在分配对话框中选择任何有效的特定于应用程序角色 （如果可用）。 具有“默认访问权限”  角色的用户排除在预配之外。

* 若要确保在用户角色分配 Smartsheet 与 Azure AD 之间的奇偶校验，它被建议使用完整的 Smartsheet 用户列表中填充相同的角色分配。 若要从 Smartsheet 中检索此用户列表，请导航到**帐户管理员 > 用户管理 > 更多操作 > 下载用户列表 (csv)** 。

* 若要访问该应用程序中的某些功能，Smartsheet 要求用户具有多个角色。 若要了解有关用户的类型和 Smartsheet 中的权限的详细信息，请转到[用户类型和权限](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)。

*  如果用户具有多个角色分配中 Smartsheet，你**必须**确保在 Azure AD，以避免用户可能永久失去对 Smartsheet 对象的访问权限的方案中复制这些角色分配的。 Smartsheet 中的每个唯一角色**必须**在 Azure AD 中分配给不同的组。 用户**必须**则添加到每个对应于所需的角色组。 

## <a name="set-up-smartsheet-for-provisioning"></a>为预配设置 Smartsheet

配置自动用户预配 Azure AD 与 Smartsheet 之前, 将需要启用上 Smartsheet SCIM 预配。

1. 以身份登录**SysAdmin**中 **[Smartsheet 门户](https://app.smartsheet.com/b/home)** 并导航到**帐户管理员**。

    ![Smartsheet 帐户管理员](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 转到**安全控制 > 用户自动预配 > 编辑**。

    ![Smartsheet 安全控制](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 添加并验证您计划从到 Smartsheet 的 Azure AD 预配的用户的电子邮件域。 选择**未启用**以确保所有预配操作仅来自从 Azure AD，并还确保你的 Smartsheet 用户列表与 Azure AD 分配同步。

    ![Smartsheet 用户预配](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 验证完成后，必须将激活域。 

    ![Smartsheet 激活域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 生成**机密令牌**配置自动用户预配与 Azure AD 通过导航到所需**应用程序和集成**。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 选择**API 访问**。 单击**生成新的访问令牌**。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定义 API 的访问令牌的名称。 单击 **“确定”** 。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 复制 API 访问令牌并将其保存，因为它是唯一一次可以查看它。 这必须执行**机密令牌**Azure AD 中的字段。

    ![Smartsheet 令牌](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>从库中添加 Smartsheet

若要配置自动用户预配 Azure AD 与 Smartsheet，需要将 Smartsheet 从 Azure AD 应用程序库添加到托管 SaaS 应用程序的列表。

1. 在中 **[Azure 门户](https://portal.azure.com)** ，在左侧的导航窗格中，选择**Azure Active Directory**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新的应用程序，请选择**新的应用程序**窗格顶部的按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Smartsheet**，选择**Smartsheet**在结果面板中。 

    ![结果列表中的 Smartsheet](common/search-new-app.png)

5. 选择**Smartsheet 注册**按钮，这将重定向到 Smartsheet 的登录页。 

    ![Smartsheet OIDC 添加](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet 是 OpenIDConnect 应用，选择将登录到 Smartsheet 使用 Microsoft 工作帐户。

    ![Smartsheet OIDC 登录名](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 身份验证成功后，接受同意页的许可提示。 然后将会应用程序自动添加到你的租户，你将重定向到你的 Smartsheet 帐户。

    ![Smartsheet OIDc 同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>配置自动用户预配到 Smartsheet 

本部分将指导你完成步骤以配置 Azure AD 预配服务以创建、 更新和禁用用户和/或在 Azure AD 中基于用户和/或组分配 Smartsheet 中的组。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>若要配置自动用户在 Azure AD 中预配的 Smartsheet:

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**的所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**Smartsheet**。

    ![应用程序列表中的 Smartsheet 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![预配选项卡](common/provisioning-automatic.png)

5. 下**管理员凭据**部分中，输入`https://scim.smartsheet.com/v2/`中**租户 URL**。 输入用于检索和保存之前从 Smartsheet 中的值**机密令牌**。 单击**测试连接**以确保 Azure AD 可以连接到 Smartsheet。 如果连接失败，请确保你的 Smartsheet 帐户具有 SysAdmin 权限，然后重试。

    ![令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”  。

8. 下**映射**部分中，选择**Azure Active Directory 用户同步到 Smartsheet**。

    ![Smartsheet 用户映射](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. 查看从 Azure AD 同步到 Smartsheet 中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配 Smartsheet 中的用户帐户，以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Smartsheet 用户属性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要启用 Azure AD 预配 Smartsheet 的服务，更改**预配状态**到**上**中**设置**部分。

    ![预配状态切换](common/provisioning-toggle-on.png)

12. 选择所需的值中预配到 Smartsheet 中定义的用户和/或组你想**作用域**中**设置**部分。

    ![预配作用域](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![正在保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告，其中描述了由 Azure AD 预配服务对 Smartsheet 执行所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Smartsheet 不支持软删除。 当用户**active**属性设置为 False，Smartsheet 会永久删除用户。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
