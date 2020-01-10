---
title: 教程：通过 Azure Active Directory 为自动用户预配配置工具 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以便自动预配和取消设置要提供的用户帐户。
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
ms.openlocfilehash: 9d00024351c18789e26120cc2af006b9aac4232d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767834"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教程：为自动用户预配配置工具

本文介绍如何配置 Azure Active Directory （Azure AD）以自动预配和取消设置要控制的用户或组。

> [!NOTE]
> 本文介绍了在 Azure AD 用户预配服务的基础上构建的连接器。 有关此服务的重要信息以及常见问题的答案，请参阅[利用 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](../manage-apps/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

本文中所述的方案假定你已具备以下先决条件：

* Azure AD 租户
* [工具租户](https://harness.io/pricing/)
* 使用*管理员*权限的环境中的用户帐户

## <a name="assign-users-to-harness"></a>将用户分配到工具

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，决定 Azure AD 中的哪些用户或组需要访问功能。 然后，你可以按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明分配这些用户或组。

## <a name="important-tips-for-assigning-users-to-harness"></a>将用户分配到工具的重要提示

* 建议分配单个 Azure AD 用户来测试自动用户预配配置。 稍后可以分配其他用户或组。

* 将用户分配到工具时，必须在**分配**对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-harness-for-provisioning"></a>设置用于预配的工具

1. 登录到你的[工具管理控制台](https://app.harness.io/#/login)，然后访问 "**持续安全** > **访问管理**"。

    ![工具管理控制台](media/harness-provisioning-tutorial/admin.png)

1. 选择 " **API 密钥**"。

    ![工具 API 密钥链接](media/harness-provisioning-tutorial/apikeys.png)

1. 选择 "**添加 API 密钥**"。 

    ![工具添加 API 密钥链接](media/harness-provisioning-tutorial/addkey.png)

1. 在 "**添加 Api 密钥**" 窗格中，执行以下操作：

    ![工具添加 Api 密钥窗格](media/harness-provisioning-tutorial/title.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**名称**" 框中，为密钥提供名称。  
   b.保留“数据库类型”设置，即设置为“共享”。 在 "**继承自**" 下拉列表中，选择一个选项。 
   
1. 选择“提交”。

1. 复制**密钥**以便以后在本教程中使用。

    ![工具创建令牌](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>从库中添加工具

在配置用于 Azure AD 的自动用户预配的工具之前，需要将 Azure AD 应用程序库中的工具集添加到托管 SaaS 应用程序列表。

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”。

    ![“所有应用程序”链接](common/enterprise-applications.png)

1. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中，输入 "**工具**"，在结果列表中选择 "**工具**"，然后选择 "**添加**" 按钮添加该应用程序。

    ![结果列表中的工具](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>配置自动用户预配以进行配 

本部分将指导你完成配置 Azure AD 预配服务，以便基于 Azure AD 中的用户或组分配来创建、更新和禁用环境中的用户或组的步骤。

> [!TIP]
> 还可以按照 "[工具单一登录" 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)中的说明，选择启用基于 SAML 的单一登录。 你可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要了解有关工具 SCIM 终结点的详细信息，请参阅工具[API 密钥](https://docs.harness.io/article/smloyragsm-api-keys)这篇文章。

若要为 Azure AD 中的工具配置自动用户预配，请执行以下操作：

1. 在[Azure 门户](https://portal.azure.com)中，选择 "**企业应用程序**" > "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Harness”。

    ![应用程序列表中的 "工具" 链接](common/all-applications.png)

1. 选择 "**预配**"。

    !["设置" 按钮](common/provisioning.png)

1. 在 "**预配模式**" 下拉列表中，选择 "**自动**"。

    !["预配模式" 下拉列表](common/provisioning-automatic.png)

1. 在 "**管理员凭据**" 下，执行以下操作：

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)
 
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**租户 URL** " 框中，输入 **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** 。  
   b.保留“数据库类型”设置，即设置为“共享”。 在 "**机密令牌**" 框中，输入你在 "设置用于预配的工具" 部分的步骤6中保存的 SCIM Authentication 令牌值。  
   c. 选择 "**测试连接**" 以确保 Azure AD 可以连接到工具。 如果连接失败，请确保你的工具帐户具有*管理员*权限，然后重试。

1. 在 "**通知电子邮件**" 框中，输入应接收预配错误通知的人员或组的电子邮件地址，然后选中 "**发生故障时发送电子邮件通知**" 复选框。

    !["通知电子邮件" 框](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在 "**映射**" 下，选择 "**同步用户要利用的 Azure Active Directory**。

    ![利用 "将 Azure Active Directory 用户同步到工具" 链接](media/harness-provisioning-tutorial/usermappings.png)

1. 在 "**属性映射**" 下，查看从 Azure AD 同步到工具的用户属性。 选为 "*匹配*" 的属性用于匹配用于更新操作的工具中的用户帐户。 选择“保存”，提交所有更改。

    ![工具用户 "属性映射" 窗格](media/harness-provisioning-tutorial/userattributes.png)

1. 在 "**映射**" 下，选择 "**同步 Azure Active Directory 组" 以进行控制**。

    ![利用 "将 Azure Active Directory 组同步到工具" 链接](media/harness-provisioning-tutorial/groupmappings.png)

1. 在 "**属性映射**" 下，查看从 Azure AD 同步到工具的组属性。 选为 "*匹配*" 属性的属性用于匹配用于更新操作的工具中的组。 选择“保存”，提交所有更改。

    ![工具组 "属性映射" 窗格](media/harness-provisioning-tutorial/groupattributes.png)

1. 若要配置范围筛选器，请参阅[基于属性的应用程序预配和范围筛选器](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

1. 在 "设置" 下，为 "**设置**" 启用 "Azure AD 预配服务"，将**设置状态**切换到 **"打开**"。

    ![预配状态切换切换到 "打开"](common/provisioning-toggle-on.png)

1. 在 "**设置**" 下的 "**作用域**" 下拉列表中，选择要将正在设置的用户或组同步到的方式。

    ![预配范围](common/provisioning-scope.png)

1. 准备好进行预配时，请选择 "**保存**"。

    !["设置" "保存" 按钮](common/provisioning-configuration-save.png)

此操作将启动正在设置的用户或组的初始同步。 初始同步执行的时间比更新时间长。 只要 Azure AD 预配服务正在运行，就大约每40分钟执行一次同步。 若要监视进度，请参阅 "**同步详细信息**" 部分。 你还可以跟踪到预配活动报告的链接，该报告描述了在工具上 Azure AD 预配服务执行的所有操作。

有关如何读取 Azure AD 设置日志的详细信息，请参阅有关[自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
