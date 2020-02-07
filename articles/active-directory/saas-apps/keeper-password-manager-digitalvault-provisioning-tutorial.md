---
title: 教程：配置 Keeper 密码管理器 & 用于 Azure Active Directory 的自动用户预配的数字保管库 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动将用户帐户预配到 Keeper 密码管理器并取消其预配 & 数字保管库。
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057494"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Keeper 密码管理器 & 数字保管库

本教程的目的是演示要在 Keeper 密码管理器 & 数字保管库和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到 Keeper 密码管理器 & 数字保管库。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [& 数字保管库租户的 Keeper 密码管理器](https://keepersecurity.com/pricing.html?t=e)
* 使用管理员权限 & 的数字保管库中的用户帐户 Keeper 密码管理器。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>从库中添加 Keeper 密码管理器 & 数字保管库

在配置 Keeper 密码管理器 & 通过 Azure AD 自动用户预配之前，需要将 Azure AD 应用程序库中的 Keeper 密码管理器 & 数字保管库添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Keeper 密码管理器 & 数字保管库，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Keeper 密码管理器 & 数字保管库**"，在结果面板中选择 " **Keeper Password Manager & 数字保管库**"，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>将用户分配到 Keeper 密码管理器 & 数字保管库

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问 Keeper 密码管理器 & 数字保管库。 确定后，可按照此处的说明将这些用户和/或组分配给 Keeper 密码管理器 & 数字保管库：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>将用户分配到 Keeper 密码管理器 & 数字保管库的重要提示

* 建议将单个 Azure AD 用户分配给 Keeper 密码管理器 & 数字保管库来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 & 数字保管库的 Keeper 密码管理器时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>& 数字保管库配置自动用户预配以 Keeper 密码管理器 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Keeper 密码管理器 & 数字保管库中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择根据 Keeper 密码管理器中提供的说明 & 数字保管库启用基于 SAML 的单一登录[& 数字保管库单一登录教程](keeperpasswordmanager-tutorial.md)。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>若要在 Azure AD 中配置 Keeper 密码管理器的自动用户预配 & 数字保管库：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Keeper Password Manager 和 Digital Vault”。

    ![应用程序列表中的 Keeper Password Manager & Digital Vault 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下，输入 Keeper 密码管理器的**租户 URL**和**机密令牌**& 数字保管库的帐户，如步骤6中所述。

6. 登录到[Keeper 管理控制台](https://keepersecurity.com/console/#login)。 单击 "**管理**" 并选择现有节点或新建一个节点。 导航到 "**预配**" 选项卡，然后选择 "**添加方法**"。

    ![Keeper 管理控制台](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    选择**SCIM （系统以实现跨域标识管理**。

    ![Keeper 添加 SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    单击 "**创建预配令牌**"。

    ![Keeper 创建终结点](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    复制**URL**和**令牌**的值，并将其粘贴到 AZURE AD 中的**租户 URL**和**机密令牌**。 单击 "**保存**" 以完成 Keeper 上的设置设置。

    ![Keeper 创建令牌](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 填充步骤5中所示的字段后，请单击 "**测试连接**" 以确保 Azure AD 可以连接到 Keeper 密码管理器 & 数字保管库。 如果连接失败，请确保 Keeper 密码管理器 & 数字保管库帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击 **“保存”** 。

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Keeper 密码管理器 & 数字保管库**"。

    ![Keeper 用户映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Keeper 密码管理器 & 数字保管库的用户属性。 选为 "**匹配**" 属性的属性用于匹配 Keeper 密码管理器 & 数字保管库中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Keeper 用户属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 Keeper 密码管理器 & 数字保管库**"。

    ![Keeper 组映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Keeper 密码管理器 & 数字保管库的组属性。 选为 "**匹配**" 属性的属性用于匹配 Keeper 密码管理器 & 数字保管库中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Keeper 组属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 若要为 Keeper 密码管理器 & 数字保管库启用 Azure AD 预配服务，请在 "**设置**" 部分中将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

16. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Keeper 密码管理器 & 数字保管库的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

17. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务在 Keeper 密码管理器 & 数字保管库上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Keeper 密码管理器 & 数字保管库要求**电子邮件**和**用户名**具有相同的源值，因为任何一个属性的任何更新都将修改其他值。
* Keeper 密码管理器 & 数字保管库不支持用户删除，请仅禁用。 已禁用的用户将在 Keeper 管理控制台 UI 中显示为 "已锁定"。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

