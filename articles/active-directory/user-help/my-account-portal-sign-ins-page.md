---
title: 从"我的登录（预览）"页面查看和搜索最近的登录活动 - Azure 活动目录 |微软文档
description: 有关如何从"我的帐户"门户的"我的登录"页面查看和搜索您最近的登录活动的详细信息。
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
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064013"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>从"我的登录（预览）"页面查看和搜索您最近的登录活动

您可以在 **"我的帐户"** 门户的"**我的登录"** 页面查看所有最近的工作或学校帐户登录活动。 查看登录历史记录可帮助您查看异常活动，从而帮助您查看：

- 如果有人试图猜测您的密码。

- 如果攻击者成功登录到您的帐户，以及从什么位置登录。

- 攻击者尝试访问哪些应用。

## <a name="view-your-recent-sign-in-activity"></a>查看近期的登录活动

1. 登录到工作或学校帐户，然后转到 https://myprofile.microsoft.com/ 页。

2. 从左侧导航窗格中选择 **"我的登录（预览）"，** 或从"**我的登录（预览）"** 块中选择 **"查看最近的活动**"链接。

    ![我的帐户页面，显示突出显示的"最近活动"链接](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展开并查看每个登录项，确保辨认得出每一项。 如果您发现一个看起来不熟悉的登录项目，我们强烈建议您更改密码，以帮助保护您的帐户，如果它被泄露。

    ![包含展开的登录详细信息的近期活动页面](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果您看到成功登录

你应该认识到自己的活动是正常的。 但是，如果您注意到来自陌生位置、浏览器或操作系统的成功登录，则可能意味着攻击者已访问您的帐户。 在此情况下，我们建议您立即更改密码，然后转到["安全信息](https://mysignins.microsoft.com/security-info)"页面以更新安全设置。

在确定某些内容不正确之前，请确保您没有看到误报（项目看起来有问题，但可以）。 例如，我们会根据您的 IP 地址确定您的大致位置和地图。 移动网络尤其难以精确定位，因为它们有时会将流量路由到遥远的位置。 因此，如果您在华盛顿州使用移动设备登录，则该位置可能会显示来自加利福尼亚州的登录。 因此，我们强烈建议您查看更多详细信息，而不仅仅是位置。 您还应确保操作系统、浏览器和应用程序都有意义。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果您看到登录失败

登录失败，没有会话活动，意味着您的主要验证方法（用户名/密码）失败。 这可能意味着您键入的用户名或密码错误，但也可能意味着攻击者正在尝试猜测您的密码。 如果您认为攻击者试图猜测您的密码失败，则不必更改密码，但我们强烈建议您注册 Azure 多重身份验证 （MFA）。 使用 MFA，即使黑客最终猜到您的密码，也不足以访问您的帐户。

如果您看到登录失败，在"会话活动"下有注释，上面写着"**其他验证失败，代码无效**"，则意味着主身份验证（用户名/密码）成功，但 MFA 失败。 如果这是攻击者，他们正确猜到了您的密码，但仍无法通过 MFA 质询。 在这种情况下，我们建议您仍更改密码，因为攻击者正确获取了该部分，然后转到["安全信息](https://mysignins.microsoft.com/security-info)"页以更新您的安全设置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登录活动

您可以通过任何可用信息搜索最近的登录活动。 例如，您可以按操作系统、位置、应用等搜索最近的登录活动。

1. 在"**查看最近活动"** 页上，在 **"搜索"** 栏中键入要搜索的信息。 例如，键入`My Account`以搜索"我的帐户"应用收集的所有活动。

2. 选择 **"搜索"** 按钮以开始搜索。

    ![最近活动页面，显示突出显示的搜索栏、搜索按钮和结果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>后续步骤

查看最近的登录活动后，您可以：

- 查看或管理[您的安全信息](user-help-security-info-overview.md)。

- 查看或管理连接[的设备](my-account-portal-devices-page.md)。

- 查看或[管理您的组织。](my-account-portal-organizations-page.md)

- 查看您的组织如何使用[您的隐私相关数据](my-account-portal-privacy-page.md)。
