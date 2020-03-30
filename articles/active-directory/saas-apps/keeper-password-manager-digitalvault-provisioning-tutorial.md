---
title: 教程：配置保管人密码管理器&数字保管库，以便使用 Azure 活动目录自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消将用户帐户预配到保管人密码管理器&数字保管库。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057494"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>教程：为自动用户预配配置保管员密码管理器&数字保管库

本教程的目的是演示在"守护者密码管理器&数字保管库和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向保管人密码管理器&数字保管库预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [保管人密码管理器&数字保管库租户](https://keepersecurity.com/pricing.html?t=e)
* 管理员密码管理器中的用户帐户&具有管理员权限的数字保管库。

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>从库中添加保管员密码管理器&数字保管库

在将保管人密码管理器&数字保管库配置为使用 Azure AD 进行自动用户预配之前，需要将 Keeper 密码管理器& Azure AD 应用程序库中的数字保管库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加保管人密码管理器&数字保管库，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在"**数字保管库"&输入"保管人密码管理器**"，在结果面板中选择 **"保管人密码管理器&数字保管库**"，然后单击"**添加**"按钮以添加应用程序。

    ![结果列表中的 Keeper Password Manager & Digital Vault](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>将用户分配给保管员密码管理器&数字保管库

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 Keeper 密码管理器&数字保管库。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给管理员密码管理器&数字保管库：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>将用户分配给 &数字保管库的管理员密码管理器的重要提示

* 建议将单个 Azure AD 用户分配给保管人密码管理器&数字保管库以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 &数字保管库的保管人密码管理器时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>将自动用户预配配置配置给保管人密码管理器&数字保管库 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 Keeper 密码管理器&数字保管库中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以根据["保管人密码管理器&数字保管库单一登录教程](keeperpasswordmanager-tutorial.md)"中提供的说明，选择启用基于 SAML 的单一登录状态的保管人密码管理器&数字保管库。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>要为 Azure AD 中的保管人密码管理器&数字保管库配置自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Keeper Password Manager 和 Digital Vault”****。

    ![应用程序列表中的 Keeper Password Manager & Digital Vault 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理员凭据"** 部分下，输入管理员密码管理器的**租户 URL**和**机密令牌**&数字保管库帐户，如步骤 6 所述。

6. 登录到[您的管理员管理控制台](https://keepersecurity.com/console/#login)。 单击 **"管理员"** 并选择现有节点或创建新节点。 导航到**预配**选项卡，然后选择 **"添加方法**"。

    ![管理员管理控制台](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    选择**SCIM（跨域标识管理系统**）。

    ![守护者添加 SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    单击 **"创建预配令牌**"。

    ![保管人创建终结点](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    复制**URL**和**令牌**的值，并将其粘贴到 Azure AD 中的**租户 URL**和**秘密令牌**中。 单击 **"保存**"以完成 Keeper 上的预配设置。

    ![保管人创建令牌](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 填充步骤 5 中显示的字段后，单击 **"测试连接**"以确保 Azure AD 可以连接到 &数字保管库的保管人密码管理器。 如果连接失败，请确保您的保管人密码管理器&数字保管库帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“保存”。****

10. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到"数字保管库"&管理员密码管理器**。

    ![管理员用户映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. 在**属性映射**部分中查看从 Azure AD 同步到保管人密码管理器&数字保管库的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Keeper 密码管理器&数字保管库中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![管理员用户属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到"数字保管库"&管理员密码管理器**。

    ![保管人组映射](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. 在**属性映射**部分中查看从 Azure AD 同步到保管人密码管理器&数字保管库的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Keeper 密码管理器&数字保管库中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![保管人组属性](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 要为保管人密码管理器启用 Azure AD 预配服务&数字保管库，请将 **"设置"** 部分中的**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

16. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配给保管人密码管理器&数字保管库的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

17. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 Keeper 密码管理器 &数字保管库上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* &数字保管库的保管人密码管理器要求**电子邮件**和**用户名**具有相同的源值，因为对任一属性的任何更新都将修改其他值。
* 保管员密码管理器&数字保管库不支持用户删除，仅禁用。 已禁用的用户将显示在管理员管理控制台 UI 中锁定。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

