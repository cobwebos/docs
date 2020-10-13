---
title: 使用智能锁定 Azure Active Directory 阻止攻击
description: 了解 Azure Active Directory 智能锁定如何帮助保护组织免受尝试猜测用户密码的暴力攻击。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: baffe307a560f2668c2d93e36939a695cf963e89
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968372"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>利用 Azure Active Directory 智能锁定保护用户帐户

智能锁定有助于锁定错误的执行组件，这些执行组件尝试猜测用户的密码或使用强制方法获取。 智能锁定可以识别来自有效用户的登录，并将其视为不同于攻击者和其他未知源的登录。 攻击者会被锁定，而你的用户将继续访问其帐户并提高工作效率。

## <a name="how-smart-lockout-works"></a>智能锁定的工作原理

默认情况下，智能锁定会在 10 次尝试失败后锁定帐户，使其在一分钟内无法进行登录尝试。 在每次后续登录尝试失败后，帐户会再次锁定，第一次锁定一分钟，后续尝试失败会锁定更长时间。 若要最大程度地减少攻击者可以绕过此行为的方法，我们不会公开锁定时间段在超过其他不成功登录尝试的速率。

智能锁定跟踪最后三个错误的密码哈希，以避免对相同密码增大锁定计数器。 如果某人多次输入同一错误密码，则此行为不会导致帐户锁定。

> [!NOTE]
> 哈希跟踪功能不可用于启用直通身份验证的客户，因为身份验证在本地进行，而不是在云中。

使用 AD FS 2016 和 AF FS 2019 的联合部署可使用 [AD FS Extranet 锁定和 Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)实现类似的好处。

对于所有 Azure AD 的客户，智能锁定始终处于开启状态，这些默认设置提供适当的安全和可用性组合。 自定义智能锁定设置（其中包含特定于组织的值）需要为用户 Azure AD Premium P1 或更高版本的许可证。

使用智能锁定并不保证真正的用户永远不会被锁定。当智能锁定锁定用户帐户时，我们将尽力尝试不锁定正版用户。 锁定服务尝试确保不良执行组件无法访问正版用户帐户。 请注意以下事项：

* 每个 Azure AD 数据中心单独跟踪锁定。 如果用户点击了每个数据中心，则用户 (*threshold_limit * datacenter_count*) 尝试次数。
* 智能锁定使用熟悉的位置与不熟悉的位置来区分不良参与者与真正的用户。 不熟悉和熟悉的位置都有单独的锁定计数器。

智能锁定可与使用密码哈希同步或传递身份验证的混合部署集成，以保护本地 Active Directory 域服务 (AD DS) 的帐户被攻击者锁定。 通过在 Azure AD 适当地设置智能锁定策略，可以在将攻击传播到本地 AD DS 之前对其进行筛选。

使用 [传递身份验证](../hybrid/how-to-connect-pta.md)时，请注意以下事项：

* Azure AD 锁定阈值 **小于** AD DS 帐户锁定阈值。 设置这些值，以便 AD DS 帐户锁定阈值至少大于 Azure AD 锁定阈值的两倍或三倍。
* Azure AD 锁定持续时间设置的时间必须长于 AD DS 重置帐户锁定计数器的持续时间。 Azure AD 持续时间以秒为单位，而 AD 持续时间设置为分钟。

例如，如果你希望 Azure AD 计数器高于 AD DS，则 Azure AD 为120秒 (2 分钟) 而本地 AD 设置为1分钟 (60 秒) 。

> [!IMPORTANT]
> 目前，如果用户的云帐户已被智能锁定功能锁定，管理员将无法解除其锁定。 管理员必须等到锁定持续时间到期。 但是，用户可以使用自助密码重置 (SSPR) 从受信任的设备或位置进行解锁。

## <a name="verify-on-premises-account-lockout-policy"></a>验证本地帐户锁定策略

若要验证本地 AD DS 帐户锁定策略，请使用管理员权限从已加入域的系统中完成以下步骤：

1. 打开“组策略管理”工具。
2. 编辑包含组织的帐户锁定策略的组策略，例如 " **默认域策略**"。
3. 浏览到 "**计算机配置**  >  **策略**" "  >  **Windows 设置**" "帐户" "帐户  >  **Security Settings**  >  **Account Policies**  >  **锁定策略**"。
4. 验证 " **帐户锁定阈值** " 和 " **重置帐户锁定计数器** " 值。

![修改本地 Active Directory 帐户锁定策略](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智能锁定值

根据组织要求，你可以自定义 Azure AD 智能锁定值。 自定义智能锁定设置（其中包含特定于组织的值）需要为用户 Azure AD Premium P1 或更高版本的许可证。

若要检查或修改组织的智能锁定值，请完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 " *Azure Active Directory*"，然后选择 "**安全**  >  **身份验证方法**  >  **密码保护**"。
1. 根据帐户在第一次锁定之前允许的登录失败次数，设置“锁定阈值”****。

    默认值为 10。

1. 将“锁定持续时间(以秒计)”设置为每次锁定的时长（以秒计）****。

    默认值为 60 秒（一分钟）。

> [!NOTE]
> 如果锁定后的首次登录也失败了，则帐户再次锁定。 如果帐户重复锁定，则锁定持续时间增加。

![在 Azure 门户中自定义 Azure AD 智能锁定策略](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>如何确定智能锁定功能是否正常工作

触发智能锁定阈值时，帐户被锁定时，会收到以下消息：

*你的帐户已暂时锁定，以防止未经授权的使用。请稍后重试，如果问题仍然存在，请与管理员联系。*

## <a name="next-steps"></a>后续步骤

若要进一步自定义体验，可以 [为 Azure AD 密码保护配置自定义禁止密码](tutorial-configure-custom-password-protection.md)。

若要帮助用户在 web 浏览器中重置或更改其密码，可以 [配置 Azure AD 自助密码重置](tutorial-enable-sspr.md)。
