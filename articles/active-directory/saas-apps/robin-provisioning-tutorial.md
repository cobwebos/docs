---
title: 教程：通过 Azure Active Directory 配置自动用户预配的机制 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动预配和取消预配用户帐户，以使其处于开启状态。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060926"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>教程：为自动用户预配配置调度

本教程的目的是演示要在操作中执行的步骤，并 Azure Active Directory （Azure AD）将 Azure AD 自动预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [一个循租户](https://robinpowered.com/pricing/)
* 具有管理员权限的用户帐户。

## <a name="assigning-users-to-robin"></a>将用户分配到机制

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问权限。 确定后，可按照此处的说明将这些用户和/或组分配给循机制：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>将用户分配到机制的重要提示

* 建议为单个 Azure AD 用户分配一个机制来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给用户时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-robin-for-provisioning"></a>设置用于预配的机制

1. 登录到 "[管理控制台](https://dashboard.robinpowered.com/login)"。 导航到 "**管理" > 集成 > SCIM > 管理**"。

    ![开启机制的管理控制台](media/robin-provisioning-tutorial/robin-admin.png)

2.  生成新的组织令牌。 如果你丢失此令牌，你始终可以创建一个新的令牌而不影响现有用户。

    ![调度动力添加 SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  复制**SCIM 身份验证令牌**。 此值将输入到 Azure 门户中的 "设置" 选项卡的 "设置" 选项卡的 "机密令牌" 字段中。



## <a name="add-robin-from-the-gallery"></a>从库中添加循机制

在配置 Azure AD 的自动用户预配之前，需要将 Azure AD 应用程序库中的机制添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加机制，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 "**循机制**"，在结果面板中选择 "**循机制**"，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的循机制](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>为循机制配置自动用户预配 

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配来创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 你还可以根据 "[机制单一登录" 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)中提供的说明，选择启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>为 Azure AD 中的机制配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Robin”。

    ![应用程序列表中的 "打开的机制" 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://api.robinpowered.com/v1.0/scim-2`。 输入先前在**机密令牌**中检索到的**SCIM Authentication 令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到机制。 如果连接失败，请确保你的调度帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**同步 Azure Active Directory 用户**"。

    ![开启了机制的用户映射](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 "机制" 的用户属性。 选为 "**匹配**" 属性的属性用于匹配用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![开启的用户属性](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到机制**"。

    ![机制支持的组映射](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 "机制" 的组属性。 选为 "**匹配**" 属性的属性用于匹配用于更新操作的组。 选择“保存”按钮以提交任何更改。

    ![机制支持的组属性](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为调度启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要设置的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了由 Azure AD 预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。



## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

