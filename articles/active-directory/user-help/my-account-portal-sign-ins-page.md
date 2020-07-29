---
title: 从“我的登录(预览)”页面查看和搜索最近的登录活动 - Azure Active Directory | Microsoft Docs
description: 有关如何在“我的帐户”门户的“我的登录”页中查看和搜索最近登录活动的详细信息。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744515"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>从“我的登录(预览)”页面查看和搜索最近的登录活动

可以从“我的帐户”门户的“我的登录”页中查看所有最近的工作或学校帐户登录活动。  通过复查登录历史记录来查看以下内容，有助于检查是否有异常活动：

- 是否有人尝试破解你的密码。

- 是否有攻击者成功登录你的帐户以及登录位置。

- 攻击者尝试访问的应用。

## <a name="view-your-recent-sign-in-activity"></a>查看近期的登录活动

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

2. 从左侧导航窗格中选择“我的登录(预览)”，或从“我的登录(预览)”块中选择“查看最近的活动”链接  。

    ![突出显示了最近活动链接“我的帐户”页](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展开并查看每个登录项，确保辨认得出每一项。 如果发现不熟悉的登录项，我们强烈建议你更改密码，以防被盗时帮助保护你的帐户。

    ![包含展开的登录详细信息的近期活动页面](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果发现登录成功

应将自己的活动识别为正常活动。 但是，如果你注意到从陌生位置、浏览器或操作系统成功登录，这可能表示攻击者已获得帐户的访问权限。 在这种情况下，我们建议立即更改密码，然后转到[安全信息](https://mysignins.microsoft.com/security-info)页更新安全设置。

在确定某些内容不正确之前，请确保看到的不是误报（即该项看起来可疑，但没问题）。 例如，我们根据你的 IP 地址确定你的大致位置和映射。 由于移动网络有时会将流量传送到较远的位置，因此很难精确定位。 这样的话，如果你在华盛顿州使用移动设备登录，该位置可能会显示登录来自加利福尼亚。 为此，我们强烈建议你查看更多的详细信息，不要仅就位置来判断。 此外，还应确保操作系统、浏览器和应用程序都正常。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果发现登录失败

登录失败，没有会话活动，这意味着主要验证方法（用户名/密码）失败。 这可能意味着你键入了错误的用户名或密码，但也可能表示攻击者试图破解你的密码。 如果认为是攻击者尝试破解密码失败，则无需更改密码，但强烈建议注册 Azure 多重身份验证 (MFA)。 使用 MFA 时，即使黑客最终猜到你的密码，也不能访问你的帐户。

如果发现登录失败，并且会话活动下有一条注释，显示“附加验证失败，代码无效”，这意味着主要身份验证（用户名/密码）已成功，但 MFA 失败。 如果这是攻击者，他们会正确猜出你的密码，但仍无法通过 MFA 质询。 在这种情况下，建议你仍要更改密码，因为攻击者获得了该部分的权限，然后转到[安全信息](https://mysignins.microsoft.com/security-info)页更新安全设置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登录活动

你可以通过任何可用的信息搜索最近的登录活动。 例如，可以按操作系统、位置、应用等搜索最近的登录活动。

1. 在“查看最近的活动”页上，将要搜索的信息输入到“搜索”栏中。 例如，键入 `My Account` 来搜索“我的帐户”应用收集的所有活动。

2. 选择“搜索”按钮即可开始搜索。

    ![“最近的活动”页，显示突出显示的搜索栏、搜索按钮和结果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>后续步骤

查看最近的登录活动后，可以：

- 查看或管理[安全信息](user-help-security-info-overview.md)。

- 查看或管理已连接的[设备](my-account-portal-devices-page.md)。

- 查看或管理[组织](my-account-portal-organizations-page.md)。

- 查看组织如何[使用隐私相关数据](my-account-portal-privacy-page.md)。
