---
title: 使用我的人员委托用户管理（预览）-Azure AD |Microsoft Docs
description: 使用我的员工和管理单元委托用户管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 791f2e9bf825bb0a1d1ce555c9fbd879106213df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995826"
---
# <a name="manage-your-users-with-my-staff-preview"></a>利用我的员工管理用户（预览）

我的员工允许您委托给机构，例如商店经理或团队主管，确保其员工能够访问其 Azure AD 帐户的权限。 组织可以委派常见任务，例如重置密码或将电话号码更改为团队经理，而不是依赖于中心支持人员。 使用我的员工，无法访问其帐户的用户只需点击几下鼠标就能重新获得访问权限，而无需任何支持人员或 IT 人员。

在为你的组织配置员工之前，我们建议你查看此文档以及[用户文档](../user-help/my-staff-team-manager.md)，以确保你了解此功能对用户的功能和影响。 你可以利用用户文档来训练和准备用户以获得新体验，并帮助确保成功推出。

面向用户的基于短信的身份验证是 Azure Active Directory 的一项公共预览版功能。 有关预览的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="how-my-staff-works"></a>我的员工工作方式

我的员工基于管理单元（澳大利亚），这是资源的容器，可用于限制角色分配的管理控制的作用域。 在我的工作人员中，使用了澳大利亚来定义组织用户（如商店或部门）的子集。 例如，可以将团队经理分配到其作用域为一个或多个澳大利亚的角色。 在以下示例中，用户已被授予身份验证管理角色，这三个澳大利亚是角色的作用域。 有关管理单元的详细信息，请参阅[Azure Active Directory 中的管理单元管理](directory-administrative-units.md)。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。

  * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个与订阅关联的 Azure Active Directory 租户。

  * 如果需要，请[创建一个 Azure Active Directory 租户](../fundamentals/sign-up-organization.md)或[将 Azure 订阅关联到你的帐户](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
* 需要 Azure AD 租户中的*全局管理员*权限才能启用基于 SMS 的身份验证。
* 短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 每个启用的用户必须拥有以下 Azure AD 或 Microsoft 365 许可证之一：

  * [Azure AD Premium P1 或 P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 （M365） F1 或 F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [企业移动性 + 安全性（EMS） e3 或 e5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing)或[Microsoft 365 （M365） e3 或 e5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>如何启用我的员工

配置了澳大利亚后，可以将此作用域应用于访问我的员工的用户。 只有分配有管理角色的用户才可以访问我的员工。 若要启用我的员工，请完成以下步骤：

1. 以用户管理员身份登录到 Azure 门户。
2. 浏览到**Azure Active Directory** > **用户设置** > **用户功能预览** > **管理用户功能预览设置**。
3. 在 "**管理员可以访问我的人员**" 下，您可以选择为所有用户、选定用户或没有用户访问启用。

> [!Note]
> 只有被分配了管理员角色的用户才可以访问我的员工。 如果为未分配管理员角色的用户启用我的员工，则他们将无法访问我的员工。

## <a name="conditional-access"></a>条件性访问

你可以使用 Azure AD 条件性访问策略来保护我的员工门户。 在访问我的员工之前，将其用于需要多重身份验证的任务。

强烈建议使用[Azure AD 的条件性访问策略](https://docs.microsoft.com/azure/active-directory/conditional-access/)来保护我的员工。 若要将条件访问策略应用到我的员工，必须使用 PowerShell 手动创建 "我的员工服务主体"。

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>将条件性访问策略应用于我的员工

1. 安装[Microsoft Graph Beta PowerShell cmdlet](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)。
1. 运行以下命令：

        Connect-Graph -Scopes "Directory.AccessAsUser.All"
        New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"

1. 创建适用于 "我的员工" 云应用程序的条件性访问策略。

    ![为我的员工应用创建条件性访问策略](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>使用我的员工

当用户转到我的员工时，他们将显示具有管理权限的[管理单元](directory-administrative-units.md)的名称。 在[我的员工用户文档](../user-help/my-staff-team-manager.md)中，我们将使用术语 "位置" 来表示管理单元。 如果管理员的权限没有 AU 范围，则这些权限将应用于整个组织。 启用我的人员后，已被授予并已分配有管理角色的用户可以通过[https://mystaff.microsoft.com](https://mystaff.microsoft.com)访问。 他们可以选择 AU 来查看该 AU 中的用户，并选择用户以打开其配置文件。

## <a name="reset-a-users-password"></a>重置用户密码

以下角色有权重置用户密码：

- [身份验证管理员](directory-assign-admin-roles.md#authentication-administrator)
- [特权身份验证管理员](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全局管理员](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [支持管理员](directory-assign-admin-roles.md#helpdesk-administrator)
- [用户管理员](directory-assign-admin-roles.md#user-administrator)
- [密码管理员](directory-assign-admin-roles.md#password-administrator)

在**我**的工作人员中，打开用户的配置文件。 选择“重置密码”。****

- 如果用户是仅限云的，你可以看到可向用户分配的临时密码。
- 如果用户是从本地 Active Directory 同步，则可以输入符合本地 AD 策略的密码。 然后，可以将该密码给用户。

    ![密码重置进度指示器和成功通知](media/my-staff-configure/reset-password.png)

用户下次登录时必须更改其密码。

## <a name="manage-a-phone-number"></a>管理电话号码

在**我**的工作人员中，打开用户的配置文件。

- 选择 "**添加电话号码**" 部分以添加用户的电话号码
- 选择 "**编辑电话号码**" 以更改电话号码
- 选择 "**删除电话号码**" 以删除用户的电话号码

根据您的设置，用户可以使用您设置的电话号码来登录 SMS、执行多重身份验证，以及执行自助密码重置。

若要管理用户的电话号码，必须分配有以下角色之一：

- [身份验证管理员](directory-assign-admin-roles.md#authentication-administrator)
- [特权身份验证管理员](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [全局管理员](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>搜索

你可以在 "我的工作人员" 中使用搜索栏搜索你的组织中的澳大利亚和用户。 你可以在组织中的所有澳大利亚和用户上进行搜索，但只能对你已获得管理权限的 AU 中的用户进行更改。

你还可以在 AU 中搜索用户。 为此，请使用用户列表顶部的搜索栏。

## <a name="audit-logs"></a>审核日志

可以在 Azure Active Directory 门户中查看在我的员工中执行的操作的审核日志。 如果审核日志是在我的工作人员中执行的操作生成的，则在审核事件中的 "其他详细信息" 下会显示这种情况。

## <a name="next-steps"></a>后续步骤

[我的员工用户文档](../user-help/my-staff-team-manager.md)
[管理单元文档](directory-administrative-units.md)
