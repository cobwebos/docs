---
title: 教程：为 Smartsheet 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Smartsheet 以及取消其预配。
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
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063177"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教程：为 Smartsheet 配置自动用户预配

本教程的目的是演示要在 Smartsheet 和 Azure Active Directory （Azure AD）中执行的步骤，以配置 Azure AD 自动将用户和/或组预配到 Smartsheet 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Smartsheet 租户](https://www.smartsheet.com/pricing)
* 具有系统管理员权限的 Smartsheet Enterprise 或 Enterprise Premier 计划中的用户帐户。

## <a name="assign-users-to-smartsheet"></a>将用户分配到 Smartsheet

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 Smartsheet。 确定后，可按照此处的说明将这些用户和/或组分配到 Smartsheet：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>将用户分配到 Smartsheet 的重要提示

* 建议将单个 Azure AD 用户分配到 Smartsheet 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Smartsheet 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

* 若要确保 Smartsheet 与 Azure AD 之间的用户角色分配中的奇偶校验，建议使用完全 Smartsheet 用户列表中填充的相同角色分配。 若要从 Smartsheet 中检索此用户列表，请导航到**帐户管理员 > 用户管理 > "更多操作" > 下载用户列表（csv）** 。

* 若要访问应用中的某些功能，Smartsheet 要求用户拥有多个角色。 若要详细了解 Smartsheet 中的用户类型和权限，请参阅[用户类型和权限](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)。

*  如果用户在 Smartsheet 中分配了多个角色，则**必须**确保在 Azure AD 中复制这些角色分配，以避免用户可能会永久失去对 Smartsheet 对象的访问权限。 **必须**在 Azure AD 中将 Smartsheet 中的每个唯一角色分配给不同的组。 然后，**必须**将用户添加到对应于所需角色的每个组。 

## <a name="set-up-smartsheet-for-provisioning"></a>设置 Smartsheet 以进行预配

将 Smartsheet 配置为使用 Azure AD 进行自动用户预配之前，需要在 Smartsheet 上启用 SCIM 设置。

1. 以**SysAdmin**身份登录到 **[Smartsheet 门户](https://app.smartsheet.com/b/home)** ，并导航到 "**帐户管理员**"。

    ![Smartsheet 帐户管理员](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 请参阅**安全控制 > 用户自动设置 > 编辑**"。

    ![Smartsheet 安全控件](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 添加并验证你计划从 Azure AD 到 Smartsheet 的用户的电子邮件域。 选择 "**未启用**" 以确保所有预配操作仅源自 Azure AD，并确保 Smartsheet 用户列表与 Azure AD 分配同步。

    ![Smartsheet 用户预配](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 验证完成后，你将需要激活该域。 

    ![Smartsheet 激活域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 通过导航到**应用和集成，** 生成使用 Azure AD 配置自动用户预配所需的**机密令牌**。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 选择 " **API 访问**"。 单击 "**生成新的访问令牌**"。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定义 API 访问令牌的名称。 单击“确定”。

    ![Smartsheet 安装](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 复制 API 访问令牌并将其保存，因为这只是你可以查看它的唯一时间。 这是 Azure AD 的 "**机密令牌**" 字段中的必需项。

    ![Smartsheet 标记](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>从库中添加 Smartsheet

若要为 Smartsheet 配置自动用户预 Azure AD 配，需要将 Azure AD 应用程序库中的 Smartsheet 添加到托管的 SaaS 应用程序列表。

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Smartsheet**"，在结果面板中选择 " **Smartsheet** "。 

    ![结果列表中的 Smartsheet](common/search-new-app.png)

5. 选择 "**注册 Smartsheet** " 按钮，该按钮会将你重定向到 Smartsheet 的登录页。 

    ![Smartsheet OIDC 添加](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. 由于 Smartsheet 是一个 OpenIDConnect 应用，因此请选择使用你的 Microsoft 工作帐户登录到 Smartsheet。

    ![Smartsheet OIDC 登录](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 身份验证成功后，接受同意页面的许可提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Smartsheet 帐户。

    ![Smartsheet OIDc 同意](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>配置 Smartsheet 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Smartsheet 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>若要在 Azure AD 中配置 Smartsheet 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **Smartsheet**"。

    ![应用程序列表中的 Smartsheet 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://scim.smartsheet.com/v2/`。 输入先前从 Smartsheet 中的 "**机密令牌**" 中检索并保存的值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Smartsheet。 如果连接失败，请确保 Smartsheet 帐户具有 SysAdmin 权限，然后重试。

    ![令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Smartsheet**"。

    ![Smartsheet 用户映射](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Smartsheet 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 Smartsheet 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Smartsheet 用户属性](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Smartsheet 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Smartsheet 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Smartsheet 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Smartsheet 不支持软删除。 当用户的**活动**属性设置为 False 时，Smartsheet 将永久删除该用户。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
