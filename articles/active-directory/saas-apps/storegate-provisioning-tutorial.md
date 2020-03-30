---
title: 教程：使用 Azure 活动目录配置存储门以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消预配用户帐户以存储门。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064251"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>教程：为自动用户预配配置存储门

本教程的目的是演示在应用商店门和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向应用商店门预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [存储门租户](https://www.storegate.com)
* 具有管理员权限的存储门上的用户帐户。

## <a name="assign-users-to-storegate"></a>将用户分配给应用商店门

Azure 活动目录使用称为分配的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问应用商店门。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给应用商店：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>将用户分配给应用商店门的重要提示

* 建议将单个 Azure AD 用户分配给应用商店门以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Storegate 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="set-up-storegate-for-provisioning"></a>设置用于预配的存储门

在使用 Azure AD 配置存储门以进行自动用户预配之前，需要从应用商店门检索一些预配信息。

1. 登录到[您的应用商店管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，并通过单击右上角的用户图标并选择 **"帐户设置**"导航到设置。

    ![存储门添加 SCIM](media/storegate-provisioning-tutorial/admin.png)

2. 在设置中导航到**团队>设置**，并验证切换开关是否在 **"单一登录"** 部分中打开。

    ![存储门设置](media/storegate-provisioning-tutorial/team.png)

    ![存储门切换按钮](media/storegate-provisioning-tutorial/sso.png)

3. 复制**租户 URL**和**令牌**。 这些值将分别在 Azure 门户中的应用商店门应用程序的"预配"选项卡中输入租户**URL**和**秘密令牌**字段。 

    ![存储门创建令牌](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>从库添加存储门

要配置应用商店门以使用 Azure AD 进行自动用户预配，需要将应用商店门从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 **"存储门**"，在结果面板中选择 **"存储门**"。 

    ![在结果列表中存储门](common/search-new-app.png)

5. 选择 **"商店门注册**"按钮，该按钮将重定向到应用商店门的登录页面。 

    ![存储门 OIDC 添加](media/storegate-provisioning-tutorial/signup.png)

6.  登录到[您的应用商店管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，并通过单击右上角的用户图标并选择 **"帐户设置**"导航到设置。

    ![存储门登录](media/storegate-provisioning-tutorial/admin.png)

7. 在设置中导航到 **"团队>设置**"，然后单击"单一登录"部分中的切换开关，这将启动同意流。 单击 **"激活**"。

    ![存储门团队](media/storegate-provisioning-tutorial/team.png)

    ![存储门 sso](media/storegate-provisioning-tutorial/sso.png)

    ![存储门激活](media/storegate-provisioning-tutorial/activate.png)

8. 由于应用商店门是 OpenIDConnect 应用，因此选择使用 Microsoft 工作帐户登录到应用商店门。

    ![存储门 OIDC 登录](media/storegate-provisioning-tutorial/login.png)

9. 身份验证成功后，接受同意页的同意提示。 然后，应用程序将自动添加到您的租户，您将被重定向到您的应用商店帐户。

    ![存储门 OIDc 同意](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>将自动用户预配配置为应用商店门 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在应用商店门中创建、更新和禁用用户和/或组的步骤。

> [!NOTE]
> 要了解有关应用商店门 SCIM 终结点的详细信息，请参阅[此](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)。

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>为 Azure AD 中的存储门配置自动用户预配

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**存储门**。

    !["应用程序"列表中的应用商店门链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://dialpad.com/scim`，在**租户 URL**中输入 。 输入您以前从**密令牌**中的存储门检索和保存的值。 单击 **"测试连接**"以确保 Azure AD 可以连接到应用商店门。 如果连接失败，请确保您的应用商店门帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择 **"同步 Azure 活动目录用户以存储门**"。

    ![存储门用户映射](media/storegate-provisioning-tutorial/usermappings.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到应用商店门的用户属性。 选择为 **"匹配属性"** 的属性用于与 Storegate 中的用户帐户匹配以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![存储门用户属性](media/storegate-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要启用应用商店门的 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到应用商店门的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在应用商店门上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
