---
title: 教程：为 Azure Active Directory 配置自动用户预配的闪烁 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配和取消预配到闪烁。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 9ebcbf4a-0cf9-41c3-96af-d8ab6ab11639
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e1173d663e9b80636c80717d93f75b28bc377aa9
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156119"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>教程：为自动用户预配配置闪烁

本教程的目的是演示在闪烁和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配为闪烁的用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息, 请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [闪烁的租户](https://joinblink.com/pricing)
* 使用管理员权限闪烁的用户帐户。

## <a name="assigning-users-to-blink"></a>将用户分配到闪烁

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中, 只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问闪烁。 确定后，可按照此处的说明将这些用户和/或组分配到闪烁：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>将用户分配到闪烁的重要提示

* 建议将单个 Azure AD 用户分配到闪烁，以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到闪烁时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-blink-for-provisioning"></a>设置的闪烁

1. 记录[支持案例](https://help.joinblink.com/hc/requests/new)或电子邮件**闪烁** support@joinblink.com支持，请求 SCIM 令牌。 .

2.  复制**SCIM 身份验证令牌**。 此值将在 Azure 门户中闪烁应用程序的 "预配" 选项卡的 "机密令牌" 字段中输入。

## <a name="add-blink-from-the-gallery"></a>从库中添加闪烁

在为自动用户预配配置闪烁之前 Azure AD，需要将 Azure AD 应用程序库中的闪烁添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加闪烁，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中, 选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序, 请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**闪烁**，在结果面板中选择 "**闪烁**"，然后单击 "**添加**" 按钮添加该应用程序。

    ![在结果列表中闪烁](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>将自动用户预配配置为闪烁 

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配来创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 还可以根据 "[闪烁单一登录" 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)中提供的说明，选择启用基于 SAML 的单一登录以进行闪烁。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>若要配置自动用户预配以便在 Azure AD 中闪烁：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**", 并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Blink”。

    ![应用程序列表中的闪烁链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分中`https://api.joinblink.com/scim` ，输入 "**租户 URL**"。 输入先前在**机密令牌**中检索到的**SCIM Authentication 令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到闪烁。 如果连接失败，请确保闪烁帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。

8. 在 "**映射**" 部分下，选择 "**同步 Azure Active Directory 用户以闪烁**"。

    ![闪烁用户映射](media/blink-provisioning-tutorial/User_mappings.png)

9. 查看从 Azure AD 同步到 "**属性映射**" 部分中的 "闪烁" 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 "闪烁" 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![闪烁用户属性](media/blink-provisioning-tutorial/User_attributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要使 Azure AD 预配服务闪烁，请在 "设置" 部分中将**设置** **状态**更改为 **"打开**"。

    ![设置状态切换开启](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要设置为闪烁的用户。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![正在保存设置配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务在闪烁时执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

