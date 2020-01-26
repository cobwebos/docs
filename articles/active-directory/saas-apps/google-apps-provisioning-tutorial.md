---
title: 教程：使用 Azure Active Directory 为 G Suite 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 G Suite 及如何取消预配。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d754964ac6c16c87e6f0825b4f1a955d38fa3530
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756222"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教程：为 G Suite 配置自动用户预配

本教程的目的是演示要在 G Suite 和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到 G Suite。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

> [!NOTE]
> G Suite 连接器最近于10月2019更新。 对 G Suite 连接器所做的更改包括：
> - 添加了对其他 G Suite 用户和组属性的支持。 
> - 已更新 G Suite 目标属性名称以匹配[此处](https://developers.google.com/admin-sdk/directory)定义的名称。
> - 已更新默认属性映射。

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 G Suite 的集成，需要准备好以下各项：

- Azure AD 租户
- [G Suite 租户](https://gsuite.google.com/pricing.html)
- 具有管理员权限的 G Suite 上的用户帐户。

## <a name="assign-users-to-g-suite"></a>将用户分配到 G Suite

Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中哪些用户和/或组需要访问 G Suite。 确定后，可按照此处的说明将这些用户和/或组分配到 G Suite：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>将用户分配到 G Suite 的重要提示

* 建议将单个 Azure AD 用户分配到 G Suite 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 G Suite 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-g-suite-for-provisioning"></a>设置 G Suite 进行预配

在将 G Suite 配置为使用 Azure AD 进行自动用户预配之前，需要在 G Suite 上启用 SCIM 预配。

1. 用管理员帐户登录到[G Suite 管理控制台](https://admin.google.com/)，然后选择 "**安全性**"。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。

    ![选择“安全”。][10]

1. 在“安全”页上，选择“API 参考”。

    ![选择“API 参考”。][15]

1. 选择“启用 API 访问”。

    ![选择“API 参考”。][16]

   > [!IMPORTANT]
   > 对于要预配到 G Suite 的每个用户，Azure AD 中的用户名**必须**绑定到自定义域。 例如，G Suite 不会接受 bob@contoso.onmicrosoft.com 之类的用户名， 但会接受 bob@contoso.com。 可以按照[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的说明更改现有用户的域。

1. 使用 Azure AD 添加并验证所需的自定义域后，必须使用 G Suite 再次验证它们。 若要验证 G Suite 中的域，请参阅以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在[G Suite 管理员控制台](https://admin.google.com/)中，选择 "**域**"。

    ![选择域][20]

    b.保留“数据库类型”设置，即设置为“共享”。 选择“添加域或域别名”。

    ![添加新域][21]

    c. 选择“添加另一个域”，然后键入要添加的域名。

    ![键入域名][22]

    d.单击“下一步”。 选择“继续验证域所有权”。 然后按步骤验证所拥有的域名。 有关如何通过 Google 验证你的域的全面说明，请参阅[验证站点所有权](https://support.google.com/webmasters/answer/35179)。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对要添加到 G Suite 的任何其他域重复上述步骤。

1. 接下来，确定要用于在 G Suite 中管理用户设置的管理员帐户。 导航到 "**管理员角色**"。

    ![选择 Google Apps][26]

1. 对于该帐户的**管理员角色**，请编辑该角色的**权限**。 请确保启用该帐户的所有“管理员 API 权限”，使其可用于预配。

    ![选择 Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>从库中添加 G Suite

若要为 G Suite 配置 Azure AD 的自动用户预配，需要将 Azure AD 应用程序库中的 G Suite 添加到托管的 SaaS 应用程序列表。 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中输入 " **g suite**"，在结果面板中选择 " **g suite** "，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>配置 G Suite 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 G Suite 中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择根据[g Suite 单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)中提供的说明为 g suite 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要详细了解 G Suite 的目录 API 终结点，请参阅[目录 api](https://developers.google.com/admin-sdk/directory)。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>若要在 Azure AD 中配置 G Suite 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“G Suite”。

    ![应用程序列表中的 G Suite 链接](common/all-applications.png)

1. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

1. 在“管理员凭据”部分，选择“授权”。 随即会在新的浏览器窗口中打开“Google 授权”对话框。

    ![G Suite 授权](media/google-apps-provisioning-tutorial/authorize.png)

1. 确认你要为你的 G Suite 租户授予 Azure AD 的权限。 选择“接受”。

    ![确认权限。][28]

1. 在 Azure 门户中，选择“测试连接”以确保 Azure AD 可以连接到你的应用。 如果连接失败，请确保 G Suite 帐户具有团队管理员权限。 然后再次重试“授权”步骤。

1. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 单击“ **保存**”。

1. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 G Suite**"。

    ![G Suite 用户映射](media/google-apps-provisioning-tutorial/usermappings.png)

1. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 G Suite 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 G Suite 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![G Suite 用户属性](media/google-apps-provisioning-tutorial/userattributes.png)

1. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 G Suite**"。

    ![G Suite 组映射](media/google-apps-provisioning-tutorial/groupmappings.png)

1. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 G Suite 的组属性。 选为 "**匹配**" 属性的属性用于匹配 G Suite 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。 UI 显示 Azure AD 和 G Suite 之间的一组默认属性映射。 可以通过单击 "添加新映射"，选择添加其他属性，例如组织单位。

    ![G Suite 组属性](media/google-apps-provisioning-tutorial/groupattributes.png)

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

1. 若要为 G Suite 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

1. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 G Suite 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

1. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了在 G Suite 上 Azure AD 预配服务执行的所有操作。

> [!NOTE]
> 如果用户已有使用 Azure AD 用户的电子邮件地址的现有个人/使用者帐户，则可能会在执行目录同步之前使用 Google 传输工具解决某个问题。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>常见问题
* 如果用于建立连接的帐户不是 GSuite 中的管理员，则可能会发生授权失败。 确保用于授权访问权限的帐户对用户需要预配的**所有域**都具有管理员权限。 

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
