---
title: 教程：使用 Azure 活动目录配置用于自动用户预配的线束 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消配置用户帐户以使用工具。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057800"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教程：为自动用户预配配置线束

在本文中，您将了解如何配置 Azure 活动目录 （Azure AD） 以自动预配和取消将用户或组预配到"线束"。

> [!NOTE]
> 本文介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的重要信息和常见问题解答，请参阅[使用 Azure 活动目录 自动预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本文中概述的方案假定您已经具备以下先决条件：

* Azure AD 租户
* [线束租户](https://harness.io/pricing/)
* 具有*管理员*权限的线束中的用户帐户

## <a name="assign-users-to-harness"></a>将用户分配给线束

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户或组才会同步。

在配置和启用自动用户预配之前，请决定 Azure AD 中的哪些用户或组需要访问线束。 然后，您可以按照[将用户或组分配给企业应用](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户或组分配给 Harness。

## <a name="important-tips-for-assigning-users-to-harness"></a>将用户分配给线束的重要提示

* 我们建议您为 Harness 分配单个 Azure AD 用户以测试自动用户预配配置。 稍后可以分配其他用户或组。

* 将用户分配给线束时，必须在 **"分配"** 对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有*默认访问权限*角色的用户从预配中排除。

## <a name="set-up-harness-for-provisioning"></a>设置用于预配的线束

1. 登录到[您的线束管理控制台](https://app.harness.io/#/login)，然后转到**持续安全** > **访问管理**。

    ![线束管理控制台](media/harness-provisioning-tutorial/admin.png)

1. 选择**API 密钥**。

    ![线束 API 密钥链接](media/harness-provisioning-tutorial/apikeys.png)

1. 选择 **"添加 API 密钥**"。 

    ![线束添加 API 键链接](media/harness-provisioning-tutorial/addkey.png)

1. 在 **"添加 Api 键**"窗格中，执行以下操作：

    ![线束添加 Api 键窗格](media/harness-provisioning-tutorial/title.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 **"名称"** 框中，提供键的名称。  
   b.保留“数据库类型”设置，即设置为“共享”。 在"从下拉列表中**继承的权限**"列表中，选择一个选项。 
   
1. 选择“提交”****。

1. 复制**密钥**，以便以后在本教程中使用。

    ![线束创建令牌](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>从库中添加线束

在为使用 Azure AD 自动用户预配配置使用线束之前，需要将线束从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”****。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

1. 选择**企业应用程序** > **所有应用程序**。

    ![“所有应用程序”链接](common/enterprise-applications.png)

1. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中，在结果列表中输入**线束**，然后选择 **"添加**"按钮以添加应用程序。 **Harness**

    ![结果列表中的线束](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>将自动用户配置配置为线束 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户或组分配在 Harness 中创建、更新和禁用用户或组的步骤。

> [!TIP]
> 您也可以按照[线束单登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)中的说明，选择启用基于 SAML 的单位登录。 您可以独立于自动用户预配配置单一登录，尽管这两个功能相辅相成。

> [!NOTE]
> 要了解有关线束 SCIM 终结点的更多信息，请参阅线束[API 密钥](https://docs.harness.io/article/smloyragsm-api-keys)一文。

要在 Azure AD 中配置线束的自动用户预配，请执行以下操作：

1. 在[Azure 门户](https://portal.azure.com)中，选择**企业应用程序** > **所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Harness”。****

    ![应用程序列表中的线束链接](common/all-applications.png)

1. 选择**预配**。

    ![预配按钮](common/provisioning.png)

1. 在**预配模式**下拉列表中，选择 **"自动**"。

    !["预配模式"下拉列表](common/provisioning-automatic.png)

1. 在 **"管理员凭据"** 下，执行以下操作：

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)
 
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 **"租户 URL"** 框中**`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`**，输入 。  
   b.保留“数据库类型”设置，即设置为“共享”。 在 **"秘密令牌"** 框中，输入您保存在"设置资源调配工具"部分步骤 6 中保存的 SCIM 身份验证令牌值。  
   c. 选择 **"测试连接**"以确保 Azure AD 可以连接到线束。 如果连接失败，请确保您的线束帐户具有*管理员*权限，然后重试。

1. 在 **"通知电子邮件**"框中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选择"在**发生故障时发送电子邮件通知**"复选框。

    !["通知电子邮件"框](common/provisioning-notification-email.png)

1. 选择“保存”。****

1. 在 **"映射**"下，选择**将 Azure 活动目录用户同步到线束**。

    ![利用"将 Azure 活动目录用户同步到线束"链接](media/harness-provisioning-tutorial/usermappings.png)

1. 在**属性映射**下，查看从 Azure AD 同步到线束的用户属性。 选择为 *"匹配"* 的属性用于匹配 Harness 中的用户帐户以进行更新操作。 选择“保存”，提交所有更改****。

    ![线束用户"属性映射"窗格](media/harness-provisioning-tutorial/userattributes.png)

1. 在 **"映射**"下，选择**将 Azure 活动目录组同步以进行利用**。

    ![利用"将 Azure 活动目录组同步到线束"链接](media/harness-provisioning-tutorial/groupmappings.png)

1. 在**属性映射**下，查看从 Azure AD 同步到线束的组属性。 选择为 *"匹配属性"* 的属性用于匹配线束中的组以进行更新操作。 选择“保存”，提交所有更改****。

    ![线束组"属性映射"窗格](media/harness-provisioning-tutorial/groupattributes.png)

1. 要配置范围筛选器，请参阅[使用范围筛选器的基于属性的应用程序预配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

1. 在 **"设置"** 下，要启用线束的 Azure AD 预配服务，请将**预配状态**开关切换为 **"打开**"。

    ![预配状态开关切换为"开"](common/provisioning-toggle-on.png)

1. 在 **"范围**"下拉列表中的 **"设置**"下，选择要如何同步要预配到线束的用户或组。

    ![预配范围](common/provisioning-scope.png)

1. 准备好预配时，请选择"**保存**"。

    ![预配保存按钮](common/provisioning-configuration-save.png)

此操作将开始预配的用户或组的初始同步。 初始同步执行的时间比以后的同步长。 只要 Azure AD 预配服务正在运行，大约每 40 分钟发生一次同步。 要监视进度，请转到 **"同步详细信息"** 部分。 还可以遵循指向预配活动报表的链接，该报表描述 Azure AD 预配服务在线束上执行的所有操作。

有关如何读取 Azure AD 预配日志的详细信息，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
