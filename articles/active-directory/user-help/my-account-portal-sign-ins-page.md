---
title: 从 "我的登录" 页查看和搜索最近的登录活动-Azure Active Directory |Microsoft Docs
description: 有关如何在“我的帐户”门户的“我的登录”页中查看和搜索最近登录活动的详细信息。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: 1816170da0fb6e5120f8cec6b6dda68d2ea1c678
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543162"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>从 "我的登录" 页查看和搜索最近的登录活动

可以从“我的帐户”门户的“我的登录”页中查看所有最近的工作或学校帐户登录活动。  通过复查登录历史记录来查看以下内容，有助于检查是否有异常活动：

- 是否有人尝试破解你的密码。
- 是否有攻击者成功登录你的帐户以及登录位置。
- 攻击者尝试访问的应用。

## <a name="view-your-recent-sign-in-activity"></a>查看近期的登录活动

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

2. 从左侧导航窗格中选择 "**我的登录**"，或从 "**我的登录**" 块中选择 "**查看最近的活动**" 链接。

    ![突出显示了最近活动链接“我的帐户”页](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展开并查看每个登录项，确保辨认得出每一项。 如果找到一个看起来不熟悉的登录项，请更改密码以在其泄露的情况下保护该帐户。

    ![包含展开的登录详细信息的近期活动页面](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果你看到成功登录

有时，在检查自己的正常登录活动时，可能会看到从不熟悉的位置、浏览器或操作系统的登录成功。 不熟悉的登录可能意味着攻击者已获得帐户的访问权限。 如果你看到 "未授权" 活动，建议你立即更改密码，然后转到 "[安全信息](https://mysignins.microsoft.com/security-info)" 以更新安全设置。

在确定某些内容不正确之前，请确保看到的不是误报（即该项看起来可疑，但没问题）。 例如，我们根据你的 IP 地址确定你的大致位置和映射。 由于移动网络有时会将流量传送到较远的位置，因此很难精确定位。 即使你使用华盛顿州的移动设备登录，此位置也可能会显示来自加利福尼亚的登录。 我们强烈建议你检查除位置之外的详细信息。 请确保操作系统、浏览器和应用程序都有意义。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果你看到 "登录失败"

如果你看到登录失败，这可能意味着你键入了错误的凭据。 这也可能表示攻击者试图猜测密码。 若要应对此风险，无需更改密码，但建议注册 Azure 多重身份验证（MFA）。 使用多重身份验证，即使黑客推测了密码，也不能访问帐户。

![不成功的登录磁贴](media/my-account-portal-sign-ins-page/unsuccessful.png)

如果你看到 "登录失败"，并在 "会话"**活动**下显示 "说明"， `Additional verification failed, invalid code` 则表示你的主要身份验证凭据已成功，但多重身份验证失败。 这种情况可能表示攻击者正确地猜到了你的密码，但无法通过多重身份验证质询。 建议你仍要更改密码，因为攻击者可能已有该密码，并转到 "[安全信息](https://mysignins.microsoft.com/security-info)" 页来更新安全设置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登录活动

你可以通过任何可用的信息搜索最近的登录活动。 例如，可以按操作系统、位置、应用等搜索最近的登录活动。

1. 在“查看最近的活动”页上，将要搜索的信息输入到“搜索”栏中。 例如，键入 `Unsuccessful` 以搜索我的帐户应用收集的所有失败的登录活动。

2. 选择“搜索”按钮即可开始搜索。

    ![“最近的活动”页，显示突出显示的搜索栏、搜索按钮和结果](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>确认异常活动

可以在 "**我的登录**" 页中的该活动的磁贴上确认标记为异常活动的登录。

![用于确认你执行或未尝试登录的异常登录磁贴](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>后续步骤

查看最近的登录活动后，可以：

- 查看或管理[安全信息](user-help-security-info-overview.md)。

- 查看或管理已连接的[设备](my-account-portal-devices-page.md)。

- 查看或管理[组织](my-account-portal-organizations-page.md)。

- 查看组织如何[使用隐私相关数据](my-account-portal-privacy-page.md)。

- 更改[我的帐户门户设置](my-account-portal-settings.md)
