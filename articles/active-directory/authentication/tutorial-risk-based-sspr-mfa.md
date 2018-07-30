---
title: 将基于风险的 MFA 和 SSPR 与 Azure Identity Protection 配合使用
description: 本教程介绍如何启用 Azure Identity Protection 集成，以便进行多重身份验证和自助密码重置，从而减少风险行为。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fb9ec69476253eaa559fe763dcc2c92994505602
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163252"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>教程：使用风险事件触发多重身份验证和密码更改

本教程需启用 Azure Active Directory (Azure AD) Identity Protection，这是一项 Azure AD Premium P2 功能，不仅仅是一项监视和报告工具。 若要保护组织的标识，可以配置基于风险的策略，以便自动响应风险行为。 这些策略可以进行自动阻止，也可以启动修正，包括要求进行密码更改和强制进行多重身份验证。

Azure AD Identity Protection 策略可以在现有的条件访问策略基础上使用，充当另一层保护。 用户也许从来不会触发某项需要这其中的某个策略的风险行为，但作为管理员，你知道他们是受到保护的。

可能会触发风险事件的项目包括：

* 具有已泄漏凭据的用户
* 从匿名 IP 地址登录
* 不可能前往异常位置
* 从受感染的设备登录
* 从具有可疑活动的 IP 地址登录
* 从不熟悉的位置登录

有关 Azure AD Identity Protection 的详细信息，可参阅[什么是 Azure AD Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * 启用 Azure MFA 注册
> * 启用基于风险的密码更改
> * 启用基于风险的多重身份验证

## <a name="prerequisites"></a>先决条件

* 能够访问至少分配有试用版 Azure AD Premium P2 许可证的有效 Azure AD 租户。
* 在 Azure AD 租户中具有全局管理员权限的帐户。
* 已完成前面的自助密码重置 (SSPR) 和多重身份验证 (MFA) 教程。

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>针对 SSPR 和 MFA 启用基于风险的策略

启用基于风险的策略是一个简单的过程。 以下步骤会引导你完成一个示例配置。

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>允许用户注册多重身份验证

Azure AD Identity Protection 包括一个默认策略，该策略可以让用户注册多重身份验证并轻松地标识当前注册状态。 启用此策略不会一开始就要求用户执行多重身份验证，而是会要求他们预先注册。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击“所有服务”，然后浏览到“Azure AD Identity Protection”。
1. 单击“MFA 注册”。
1. 将“强制实施策略”设置为“打开”。
   1. 设置此策略会要求所有用户注册那些准备供多重身份验证使用的方法。
1. 单击“ **保存**”。

   ![要求用户在使用 Azure AD Identity Protection 登录时注册 MFA](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>启用基于风险的密码更改

Microsoft 会与研究人员、执法机构、Microsoft 安全团队以及其他受信任的源合作，以查找用户名和密码对。 当这些对中的某一对与你环境中的某个帐户匹配时，则可通过以下策略触发基于风险的密码更改。

1. 单击“用户风险策略”。
1. 在“条件”下选择“用户风险”，然后选择“中等及以上”。
1. 单击“选择”，然后单击“完成”。
1. 在“访问权限”下选择“允许访问”，然后选择“需要密码更改”。
1. 单击“选择”
1. 将“强制实施策略”设置为“打开”。
1. 单击“保存”

### <a name="enable-risk-based-multi-factor-authentication"></a>启用基于风险的多重身份验证

大多数用户的正常行为是可以跟踪的，如果其行为超出规范，则允许他们登录可能很危险。 你可能需要阻止该用户，或者直接要求他们执行多重身份验证，证明自己真的是所宣称的用户。 若要启用一项在检测到风险登录时会要求用户执行 MFA 的策略，请启用以下策略。

1. 单击“登录风险策略”
1. 在“条件”下选择“用户风险”，然后选择“中等及以上”。
1. 单击“选择”，然后单击“完成”。
1. 在“访问权限”下选择“允许访问”，然后选择“需要多重身份验证”。
1. 单击“选择”
1. 将“强制实施策略”设置为“打开”。
1. 单击“保存”

## <a name="clean-up-resources"></a>清理资源

如果已完成测试，不再需要启用基于风险的策略，请返回到需禁用的每项策略，然后将“强制实施策略”设置为“关闭”。