---
title: 教程：使用 Azure 活动目录自动配置访问源， |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消将用户帐户预配为"访问"。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063138"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>教程：为自动用户预配配置访问

本教程的目的是演示您在访问和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户或组预配为"访问"。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的作用、工作方式以及常见问题的重要详细信息，请参阅[使用 Azure 活动目录 自动用户预配和取消预配软件即服务 （SaaS） 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息，请参阅[Microsoft Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [访问租户](https://www.visitly.io/pricing/)
* 具有管理员权限的访问中的用户帐户

## <a name="assign-users-to-visitly"></a>将用户分配到访问 

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问访问。 然后按照此处的说明将这些用户或组指定为"访问"：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>将用户分配到访问的重要提示 

* 我们建议您为访问点分配单个 Azure AD 用户以测试自动用户预配配置。 稍后可以分配其他用户或组。

* 将用户分配给"访问"时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有默认访问权限角色的用户从预配中排除。

## <a name="set-up-visitly-for-provisioning"></a>为预配设置访问

在使用 Azure AD 自动配置访问之前，需要在访问时启用跨域标识管理系统 （SCIM） 预配。

1. 登录[访问。](https://app.visitly.io/login) 选择**集成** > **主机同步**。

    ![主机同步](media/Visitly-provisioning-tutorial/login.png)

2. 选择**Azure AD**部分。

    ![Azure AD 部分](media/Visitly-provisioning-tutorial/integration.png)

3. 复制**API 密钥**。 这些值在 Azure 门户中访问应用程序"**预配**"选项卡上的 **"秘密令牌"** 框中输入。

    ![API 密钥](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>从图库中添加访问

要使用 Azure AD 自动配置访问，请将"访问"从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

要从 Azure AD 应用程序库添加访问，请按照以下步骤操作。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在"**访问"** 中输入"访问"，在结果面板中选择 **"访问"，** 然后选择 **"添加"** 以添加应用程序。

    ![结果列表中的 Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>将自动用户预配配置为访问 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户或组分配创建、更新和禁用访问中的用户或组的步骤。

> [!TIP]
> 要启用基于 SAML 的单登录访问，请按照[访问单登录教程](Visitly-tutorial.md)中的说明进行操作。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>在 Azure AD 中为访问配置自动用户预配

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序** > **所有应用程序**。

    ![所有应用程序](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Visitly”。****

    ![应用程序列表中的 Visitly 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配模式设置为"自动"](common/provisioning-automatic.png)

5. 在"管理员凭据"部分下，`https://api.visitly.io/v1/usersync/SCIM`分别输入**在租户 URL**和**秘密令牌**中检索的 和**API 密钥**值。 选择 **"测试连接**"以确保 Azure AD 可以以访问方式连接到。 如果连接失败，请确保您的访问帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在 **"通知电子邮件"** 框中，输入应接收预配错误通知的个人或组的电子邮件地址。 选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。****

8. 在 **"映射**"部分下，选择 **"同步 Azure 活动目录用户以访问**"。

    ![访问用户映射](media/visitly-provisioning-tutorial/usermapping.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到访问的用户属性。 选择为 **"匹配属性"** 的属性用于与"访问"中的用户帐户匹配以进行更新操作。 选择“保存”，提交所有更改****。

    ![访问用户属性](media/visitly-provisioning-tutorial/userattribute.png)

10. 要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

11. 要启用 Azure AD 预配服务以访问，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态切换打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到访问的用户或组。

    ![预配范围](common/provisioning-scope.png)

13. 准备好预配时，请选择"**保存**"。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作将开始**在"设置"** 部分中定义"**范围"** 中定义的所有用户或组的初始同步。 初始同步执行的时间比后续同步长。 有关用户或组预配需要多长时间的详细信息，请参阅[预配用户需要多长时间？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

可以使用"**当前状态**"部分监视进度并遵循指向预配活动报告的链接，该报表描述 Azure AD 预配服务在访问时执行的所有操作。 有关详细信息，请参阅[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要读取 Azure AD 预配日志，请参阅[报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

访问不支持硬删除。 一切都是软删除的。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
