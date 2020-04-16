---
title: 使用"我的员工"委派用户管理（预览） - Azure AD |微软文档
description: 使用"我的员工"和"管理单位"委派用户管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394210"
---
# <a name="manage-your-users-with-my-staff-preview"></a>使用"我的员工"管理您的用户（预览版）

"我的工作人员"使您能够委派权限，例如商店经理或团队主管，这些权限可确保其员工能够访问其 Azure AD 帐户。 组织可以将常见任务（如重置密码或更改电话号码）委派给团队经理，而不是依赖中央帮助台。 使用"我的员工"，无法访问其帐户的用户只需单击几下即可重新获得访问权限，无需帮助台或 IT 人员。

在为您的组织配置"我的员工"之前，我们建议您查看本文档以及[用户文档](../user-help/my-staff-team-manager.md)，以确保您了解此功能的功能和对用户的影响。 您可以利用用户文档来培训和准备用户的新体验，并帮助确保成功推出。

## <a name="how-my-staff-works"></a>我的员工如何工作

我的工作人员基于行政单位（AU），它们是一个资源容器，可用于限制角色分配管理控制的范围。 在"我的员工"中，AUS 用于定义组织用户（如商店或部门）的子集。 例如，团队经理可以分配给作用域为一个或多个 A 的的角色。 在下面的示例中，用户已被授予身份验证管理角色，并且这三个 AA 是角色的范围。 有关管理单位的详细信息，请参阅[Azure 活动目录中的管理单位管理](directory-administrative-units.md)。

## <a name="how-to-enable-my-staff"></a>如何启用我的员工

配置了 A，即可将此作用域应用于访问"我的员工"的用户。 只有分配了管理角色的用户才能访问"我的工作人员"。 要启用"我的员工"，完成以下步骤：

1. 以用户管理员身份登录到 Azure 门户。
2. 浏览到**Azure 活动目录** > **用户设置** > **用户功能预览** > **管理用户功能预览设置**。
3. 在 **"管理员可以访问我的工作人员"** 下，您可以选择为所有用户、所选用户或没有用户访问权限启用。

> [!Note]
> 只有已分配了管理员角色的用户才能访问"我的员工"。 如果您为未分配管理员角色的用户启用"我的员工"，他们将无法访问"我的员工"。

## <a name="using-my-staff"></a>使用我的员工

当用户转到"我的工作人员"时，将显示具有管理权限[的管理单位](directory-administrative-units.md)的名称。 在["我的员工"用户文档中](../user-help/my-staff-team-manager.md)，我们使用术语"位置"来指指行政单位。 如果管理员的权限没有 AU 作用域，则权限将应用于整个组织。 启用"我的员工"后，已启用并分配了管理角色的用户可以通过 访问 。 [https://mystaff.microsoft.com](https://mystaff.microsoft.com) 他们可以选择一个 AU 来查看该 AU 中的用户，并选择用户来打开其配置文件。

## <a name="licenses"></a>许可证

"我的员工"中启用的每个用户都必须获得许可，即使他们不使用"我的员工"门户。 每个启用的用户必须具有以下 Azure AD 或 Microsoft 365 许可证之一：

- Azure AD Premium P1 或 P2
- 微软 365 F1 或 F3

## <a name="reset-a-users-password"></a>重置用户密码

以下角色具有重置用户密码的权限：

- [身份验证管理员](directory-assign-admin-roles.md#authentication-administrator)
- [特权身份验证管理员](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全局管理员](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [帮助台管理员](directory-assign-admin-roles.md#helpdesk-administrator)
- [用户管理员](directory-assign-admin-roles.md#user-administrator)
- [密码管理员](directory-assign-admin-roles.md#password-administrator)

从我的**工作人员**，打开用户的个人资料。 选择“重置密码”。****

- 如果用户仅云，则可以看到可以授予用户的临时密码。
- 如果用户从本地活动目录同步，则可以输入符合本地 AD 策略的密码。 然后，您可以将该密码授予用户。

    ![密码重置进度指示器和成功通知](media/my-staff-configure/reset-password.png)

用户下次登录时必须更改其密码。

## <a name="manage-a-phone-number"></a>管理电话号码

从我的**工作人员**，打开用户的个人资料。

- 选择 **"添加电话号码**"部分可为用户添加电话号码
- 选择 **"编辑电话号码**"以更改电话号码
- 选择 **"删除电话号码**"以删除用户的电话号码

然后，根据您的设置，用户可以使用您设置的电话号码使用 SMS 登录、执行多重身份验证以及执行自助服务密码重置。

要管理用户的电话号码，必须为您分配以下角色之一：

- [身份验证管理员](directory-assign-admin-roles.md#authentication-administrator)
- [特权身份验证管理员](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全局管理员](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>搜索

您可以使用"我的员工"中的搜索栏搜索组织中的 A 和用户。 您可以在组织中的所有 AU 和用户进行搜索，但只能对已被授予管理员权限的 AU 中的用户进行更改。

您还可以在 AU 中搜索用户。 为此，请使用用户列表顶部的搜索栏。

## <a name="audit-logs"></a>审核日志

您可以查看审核日志，了解在 Azure 活动目录门户中"我的员工"中执行的操作。 如果审核日志是由"我的员工"中执行的操作生成的，您将在审核事件中的"附加详细信息"下看到此记录。

## <a name="next-steps"></a>后续步骤

[我的员工用户文档](../user-help/my-staff-team-manager.md)
[管理单位文档](directory-administrative-units.md)
