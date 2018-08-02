---
title: 教程：使用 Azure Active Directory 为 Slack 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Slack 和取消其预配。
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 9763c7a9e79f4c9e9d6296efb79e944205e8a99c
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264141"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>教程：为 Slack 配置自动用户预配


本教程的目的是说明从 Azure AD 自动将用户帐户预配到 Slack 和取消其预配所需在 Slack 和 Azure 中执行的步骤。 

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   一个 Azure Active Directory 租户
*   启用了[加法计划](https://aadsyncfabric.slack.com/pricing)或更佳计划的 Slack 租户 
*   在 Slack 中具有团队管理员权限的用户帐户 

注意：Azure AD 预配集成依赖于可供 Slack 团队在加法计划或更佳计划中使用的 [Slack SCIM API](https://api.slack.com/scim)。

## <a name="assigning-users-to-slack"></a>将用户分配到 Slack

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只有已“分配”到 Azure AD 中的应用程序的用户和组才进行同步。 

在配置和启用预配服务之前，需要确定 Azure AD 中的哪些用户和/或组表示需要访问 Slack 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Slack 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>将用户分配到 Slack 的重要提示

*   建议将单个 Azure AD 用户分配到 Slack 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Slack 时，必须在分配对话框中选择“用户”或“组”角色。 “默认访问权限”角色不可用于预配。


## <a name="configuring-user-provisioning-to-slack"></a>为 Slack 配置用户预配 

本部分指导完成将 Azure AD 连接到 Slack 的用户帐户预配 API 和配置预配服务，以便在 Slack 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

**提示：** 还可选择按照 (Azure 门户)[https://portal.azure.com] 中提供的说明为 Slack 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>若要在 Azure AD 中为 Slack 配置自动用户帐户预配，请执行以下操作：


1)  在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2) 如果已为 Slack 配置单一登录，请使用搜索字段搜索 Slack 实例。 否则，请选择“添加”并在应用程序库中搜索“Slack”。 从搜索结果中选择 Slack，并将其添加到应用程序列表。

3)  选择 Slack 实例，并选择“预配”选项卡。

4)  将“预配模式”设置为“自动”。

![Slack 预配](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  在“管理员凭据”部分下，单击“授权”。 这会在新的浏览器窗口中打开“Slack 授权”对话框。 

6) 在新窗口中，使用团队管理员帐户登录到 Slack。 在显示的授权对话框中，选择要启用预配的 Slack 团队，并选择“授权”。 完成后，返回到 Azure 门户完成预配配置。

![授权对话框](./media/slack-provisioning-tutorial/Slack3.PNG)

7) 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 Slack 应用。 如果连接失败，请确保 Slack 帐户具有团队管理员权限，并重试“授权”步骤。

8) 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

9) 单击“ **保存**”。 

10) 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Slack”。

11) 在“属性映射”部分中，查看将从 Azure AD 同步到 Slack 的用户属性。 请注意，选为**匹配**属性的属性用于匹配 Slack 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

12) 要为 Slack 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

13) 单击“ **保存**”。 

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 Slack。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 10 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Slack 应用执行的所有操作。

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[可选] 为 Slack 配置组对象预配 

（可选）可以启用从 Azure AD 到 Slack 的组对象预配。 这与“分配用户组”不同，因为除了其成员外实际组对象也会从 Azure AD 复制到 Slack。 例如，如果在 Azure AD 中有一个名为“My Group”的组，则会在 Slack 中创建相同的名为“My Group”的组。

### <a name="to-enable-provisioning-of-group-objects"></a>若要启用预配组对象，请执行以下操作：

1) 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Slack”。

2) 在“属性映射”边栏选项卡中，将“已启用”设置为“是”。

3) 在“属性映射”部分中，查看将从 Azure AD 同步到 Slack 的组属性。 请注意，选为**匹配**属性的属性用于在更新操作中匹配 Slack 中的组。 

4) 单击“ **保存**”。

这会导致“用户和组”部分中分配到 Slack 的任何组对象从 Azure AD 完全同步到 Slack。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务对 Slack 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)
