---
title: 在 "我的登录（预览）" 页中查看和搜索最近的登录活动-Azure Active Directory |Microsoft Docs
description: 有关如何在 "我的帐户" 门户的 "我的登录" 页中查看和搜索最近登录活动的详细信息。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: c828878506ee69e90945d1f4be07eb81541b9757
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625402"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>从 "我的登录（预览）" 页查看和搜索最近的登录活动

你可以从 "**我的帐户**" 门户的 "**我的登录**" 页查看你最近的工作或学校帐户登录活动。 查看您的登录历史记录有助于您查看以下内容，从而帮助您检查异常活动：

- 如果有人尝试猜测你的密码。

- 如果攻击者成功登录到你的帐户，以及从哪个位置登录。

- 攻击者尝试访问的应用。

## <a name="view-your-recent-sign-in-activity"></a>查看近期的登录活动

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

2. 从左侧导航窗格中选择 "**我的登录（预览版）** "，或从 "**我的登录（预览版）** " 块中选择 "**查看最近的活动**" 链接。

    !["我的帐户" 页，显示突出显示的最近活动链接](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展开并查看每个登录项，确保辨认得出每一项。 如果找到不熟悉的登录项，我们强烈建议您更改密码，以帮助保护您的帐户。

    ![包含展开的登录详细信息的近期活动页面](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果你看到成功登录

你应将自己的活动识别为正常。 但是，如果您注意到从奇怪的位置、浏览器或操作系统成功登录，则可能表示攻击者已获得帐户的访问权限。 在这种情况下，我们建议立即更改密码，然后转到 "[安全信息](https://mysignins.microsoft.com/security-info)" 页来更新安全设置。

确定某些内容不正确之前，请确保不会看到误报（其中项目看起来可疑，但没问题）。 例如，我们根据你的 IP 地址确定你的大致位置和映射。 移动网络特别难以确定，因为它们有时会通过远处路由流量。 因此，如果你使用华盛顿州的移动设备登录，则该位置可能显示来自加利福尼亚的登录。 为此，我们强烈建议您查看更多详细信息，而不仅仅是位置。 还应确保操作系统、浏览器和应用程序都有意义。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果你看到 "登录失败"

如果登录不成功，则不会出现会话活动，这意味着主要验证方法（用户名/密码）已失败。 这可能意味着你键入了错误的用户名或密码，但这也可能表示攻击者试图猜测你的密码。 如果认为是攻击者尝试破解密码失败，则无需更改密码，但强烈建议注册 Azure 多重身份验证（MFA）。 使用 MFA 时，即使黑客最终猜到你的密码，也不能访问你的帐户。

如果你看到 "登录失败"，则在 "会话活动" 下显示 "会话活动"，其中显示 "**其他验证失败，无效代码**"，这意味着你的主要身份验证（用户名/密码）已成功，但 MFA 失败。 如果这是攻击者，它们会正确推测你的密码，但仍无法通过 MFA 质询。 在这种情况下，我们建议你仍更改密码，因为攻击者获得了该部分的权限，然后转到 "[安全信息](https://mysignins.microsoft.com/security-info)" 页来更新安全设置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登录活动

可以通过任何可用的信息搜索最近的登录活动。 例如，你可以按操作系统、位置、应用等搜索最近的登录活动。

1. 在 "**查看最近的活动**" 页上，在**搜索**栏中键入要搜索的信息。 例如，键入`My Account`以搜索我的帐户应用收集的所有活动。

2. 选择 "**搜索**" 按钮，开始搜索。

    !["最近活动" 页，显示突出显示的搜索栏、搜索按钮和结果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>后续步骤

查看最近的登录活动后，可以：

- 查看或管理你的[安全信息](user-help-security-info-overview.md)。

- 查看或管理已连接的[设备](my-account-portal-devices-page.md)。

- 查看或管理你的[组织](my-account-portal-organizations-page.md)。

- 查看组织如何[使用与隐私相关的数据](my-account-portal-privacy-page.md)。
