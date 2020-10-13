---
title: Azure AD 中的连续访问评估
description: 使用 Azure AD 中的连续访问评估，更快地响应用户状态的更改
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1bde255355e7a4f47df6a3969837410692cef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266053"
---
# <a name="continuous-access-evaluation"></a>连续访问评估

令牌过期和刷新是业界的一种标准机制。 当客户端应用程序（如 Outlook）连接到服务（如 Exchange Online）时，API 请求通过 OAuth 2.0 访问令牌得到授权。 默认情况下，这些访问令牌的有效期为一小时，超过此时间后，客户端会被重定向回 Azure AD 来刷新它们。 该刷新期间为重新评估用户访问策略提供了机会。 例如，我们可能会选择不刷新令牌，原因是存在条件访问策略，或是用户在目录中已被禁用。 

用户的条件发生变化（例如网络位置变化或凭据被盗）后，系统需要过一段时间才会强制执行与该变化相关的策略，客户对这种时间延迟表示了担忧。 我们已经尝试了降低令牌寿命的生硬方法，但发现这种方法会降低用户体验和可靠性，而不会消除风险。

对策略冲突或安全问题的及时响应实际上需要令牌颁发者（如 Azure AD）和依赖方（如 Exchange Online）之间进行“对话”。 这种双向对话提供了两项重要功能。 信赖方可以注意到事情的变化（比如客户端来自一个新的位置），并通知令牌颁发者。 通过此对话，令牌颁发者也可通知信赖方由于帐户泄露、禁用或其他问题而停止遵从给定用户的令牌。 此对话的机制是连续访问评估 (CAE)。 虽然我们的目标是近乎实时地作出响应，但在某些情况下，由于事件传播时间的原因，延迟可能会长达 15 分钟。

连续访问评估的初始实现侧重于 Exchange、Teams 和 SharePoint Online。 

### <a name="key-benefits"></a>主要优点

- 用户终止或密码更改/重置：用户会话吊销将近乎实时地强制执行。
- 网络位置更改：条件访问位置策略将近乎实时地强制执行。
- 可以使用条件访问位置策略阻止将令牌导出到受信任网络外部的计算机。

## <a name="scenarios"></a>方案 

可以通过两种方案进行连续访问评估、关键事件评估和条件访问策略评估。

### <a name="critical-event-evaluation"></a>关键事件评估

通过允许服务（例如 Exchange Online、SharePoint Online 和 Teams）订阅 Azure AD 中的关键事件来实现连续访问评估，从而可以近乎实时地评估和强制实施这些事件。 关键事件评估不依赖于条件访问策略，因此可在任何租户中使用。 当前评估以下事件：

- 用户帐户已删除或禁用
- 用户的密码已更改或已重置
- 是否为用户启用了多重身份验证
- 管理员显式撤销用户的所有刷新令牌
- Azure AD Identity Protection 检测到提升的用户风险

此过程使用户失去了对组织的 SharePoint Online 文件、电子邮件、日历或任务以及团队的访问权限，使其能够在一个或多个关键事件之后 Microsoft 365 的客户端应用。 

### <a name="conditional-access-policy-evaluation-preview"></a>条件访问策略评估（预览版）

Exchange 和 SharePoint 能够同步关键的条件访问策略，因此可以在服务本身中对它们进行评估。

此过程可让用户在网络位置更改后立即失去对组织文件、电子邮件、日历或任务 Microsoft 365 的客户端应用或 SharePoint Online 的访问权限。

> [!NOTE]
> 并非所有应用和资源提供程序组合都受支持。 请参阅下表。 Office 指的是 Word、Excel 和 PowerPoint

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 支持 | 支持 | 不支持 | 不支持 | 支持 |
| **Exchange Online** | 支持 | 支持 | 支持 | 支持 | 支持 |

| | Office Web 应用 | Office Win32 应用 | Office for iOS | Office for Android | Office for Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 不支持 | 支持 | 支持 | 支持 | 支持 |
| **Exchange Online** | 不支持 | 支持 | 支持 | 支持 | 支持 |

### <a name="client-side-claim-challenge"></a>客户端声明质询

在进行连续访问评估之前，只要访问令牌未过期，客户端将始终试图从其缓存中重播访问令牌。 通过使用 CAE，我们引入了一种新的事例 - 即使令牌没有过期，资源提供程序也可以拒绝令牌。 为了在缓存的令牌尚未过期的情况下通知客户端绕过其缓存，我们引入了一种称为“声明质询”的机制，用来表明在令牌被拒绝的情况下需要由 Azure AD 颁发一个新的访问令牌。 CAE 要求客户端更新以理解声明质询。 以下应用程序的最新版本支持声明质询：

- Outlook Windows
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Teams for Windows（仅适用于 Teams 资源）
- Teams iOS（仅适用于 Teams 资源）
- Teams Android（仅适用于 Teams 资源）
- Teams Mac（仅适用于 Teams 资源）
- Word/Excel/PowerPoint for Windows
- Word/Excel/PowerPoint for iOS
- Word/Excel/PowerPoint for Android
- Word/Excel/PowerPoint for Mac

### <a name="token-lifetime"></a>令牌生存期

由于风险和策略是实时评估的，协商连续访问评估感知会话的客户端将依赖于 CAE，而不是现有的静态访问令牌生存期策略，这意味着协商 CAE 感知会话的支持 CAE 的客户端将不再遵守可配置令牌生存期策略。

在 CAE 会话中，令牌生存期增加，可以长时间生存，最长为 28 小时。 吊销是由关键事件和策略评估驱动的，并非可以随时吊销。 此更改提高了应用程序的稳定性，而不会影响安全状况。 

如果你不使用支持 CAE 的客户端，则默认访问令牌生存期将保持为1小时，除非已配置了具有 [可配置令牌生存期 ](../develop/active-directory-configurable-token-lifetimes.md) 的访问令牌生存期 (CTL) preview 功能。

## <a name="example-flows"></a>示例流

### <a name="user-revocation-event-flow"></a>用户吊销事件流：

![用户吊销事件流](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支持 CAE 的客户端向 Azure AD 提供凭据或刷新令牌，要求获得某个资源的访问令牌。
1. 访问令牌与其他项目一起返回到客户端。
1. 管理员显式地[撤销用户的所有刷新令牌](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 吊销事件将从 Azure AD 发送到资源提供程序。
1. 向资源提供程序提供访问令牌。 资源提供程序评估令牌的有效性，并检查用户是否存在任何吊销事件。 资源提供程序使用此信息来决定是否授予对资源的访问权限。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401+ 声明质询发送回客户端。
1. 支持 CAE 的客户端理解 401+ 声明质询。 它绕过缓存并返回到步骤 1，将其刷新令牌和声明质询一起发送回 Azure AD。 然后在此情况下，Azure AD 将重新评估所有条件，并提示用户重新进行身份验证。

### <a name="user-condition-change-flow-preview"></a>用户条件更改流（预览）：

在下面的示例中，条件访问管理员配置了一个基于位置的条件访问策略，仅允许来自特定 IP 范围的访问：

![用户条件事件流](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. 支持 CAE 的客户端向 Azure AD 提供凭据或刷新令牌，要求获得某个资源的访问令牌。
1. Azure AD 会评估所有条件访问策略，看用户和客户端是否满足条件。
1. 访问令牌与其他项目一起返回到客户端。
1. 用户从允许的 IP 范围移出
1. 客户端从允许的 IP 范围之外向资源提供程序提供访问令牌。
1. 资源提供程序评估令牌的有效性，并检查从 Azure AD 同步的位置策略。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401+ 声明质询发送回客户端，因为它不是来自允许的 IP 范围。
1. 支持 CAE 的客户端理解 401+ 声明质询。 它绕过缓存并返回到步骤 1，将其刷新令牌和声明质询一起发送回 Azure AD。 在这种情况下，Azure AD 会重新评估所有条件并拒绝访问。

## <a name="enable-or-disable-cae-preview"></a>启用或禁用 CAE（预览版）

1. 以条件访问管理员、安全管理员或全局管理员的身份登录到 **Azure 门户**
1. 浏览到“Azure Active Directory” > “安全性” > “连续访问评估”  。
1. 选择“启用预览版”。

在此页上，你可以选择对将受预览版限制的用户和组进行限制。

![在 Azure 门户中启用 CAE 预览版](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>疑难解答

### <a name="supported-location-policies"></a>支持的位置策略

对于 CAE，我们只了解基于命名 IP 的命名位置。 我们不了解其他位置设置，例如[受 MFA 信任的 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 或基于国家/地区的位置。 如果用户来自受 MFA 信任的 IP 或来自受信任的位置（其中包含受 MFA 信任的 IP 或国家/地区位置），则在用户移到其他位置之后，将不会强制执行 CAE。 在这些情况下，我们会颁发 1 小时 CAE 令牌，不进行即时 IP 强制检查。

> [!IMPORTANT]
> 在配置连续访问评估的位置时，请仅使用[基于 IP 的条件访问位置条件](../conditional-access/location-condition.md#preview-features)并配置所有 IP 地址（包括 IPv4 和 IPv6），这些地址可通过标识提供者和资源提供程序查看。 不要使用国家/地区位置条件，也不要使用 Azure 多重身份验证的服务设置页中提供的受信任的 IP 功能。

### <a name="ip-address-configuration"></a>IP 地址配置

标识提供者和资源提供程序可能会看到不同的 IP 地址。 这种不匹配可能是由组织的网络代理实现导致的，或者是由标识提供者与资源提供程序之间不正确的 IPv4/IPv6 配置导致的。 例如：

- 标识提供者看到了客户端的一个 IP 地址。
- 资源提供程序通过代理后看到了客户端的另一个 IP 地址。
- 标识提供者所看到的 IP 地址是策略中允许的 IP 范围的一部分，但来自资源提供程序的 IP 地址不是。

如果你的环境中存在此方案，为避免无限循环，Azure AD 会颁发一个有效期为一小时的 CAE 令牌，但不会强制更改客户端位置。 即使在这种情况下，与传统的一小时令牌相比，安全性也得到了提高，因为除了客户端位置更改事件之外，我们还评估[其他事件](#critical-event-evaluation)。

### <a name="office-and-web-account-manager-settings"></a>Office 和 Web 帐户管理器设置

| Office 更新通道 | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 半年企业频道 | 如果设置为 enabled 或 1，则不支持 CAE。 | 如果设置为 enabled 或 1，则不支持 CAE。 |
| 当前频道 <br> or <br> 每月企业频道 | 无论设置如何，都支持 CAE | 无论设置如何，都支持 CAE |

有关 Office 更新通道的说明，请参阅 [Microsoft 365 应用的更新通道概述](https://docs.microsoft.com/deployoffice/overview-update-channels)。 建议组织不要禁用 Web 帐户管理器 (WAM)。

### <a name="policy-change-timing"></a>策略更改计时

由于 Azure AD 与资源提供程序之间可能存在复制延迟，因此管理员所做的策略更改可能需要长达 2 小时的时间才能在 Exchange Online 上生效。

示例：管理员添加了一个策略，用于阻止某个 IP 地址范围在上午 11:00 访问电子邮件，在此之前来自该 IP 范围的用户可以继续访问电子邮件，直到下午 1:00。

### <a name="coauthoring-in-office-apps"></a>Office 应用中的共同创作

当多个用户同时在同一个文档上协作时，CAE 可能不会根据用户吊销或策略更改事件立即吊销用户对文档的访问权限。 在这种情况下，用户会在关闭文档、Word、Excel 或 PowerPoint 后或 10 小时后完全失去访问权限。

若要缩短这一时间，SharePoint 管理员可以通过[在 SharePoint Online 中配置网络位置策略](/sharepoint/control-access-based-on-network-location)，来减少存储在 SharePoint Online 和 OneDrive for Business 中的文档的共同创作会话的最大生存期。 更改此配置后，合著会话的最长生存期将减少到15分钟，并且可使用 SharePoint Online PowerShell 命令 "Set-spotenant – IPAddressWACTokenLifetime" 进一步调整。

### <a name="enable-after-a-user-is-disabled"></a>在禁用用户后启用

如果在禁用用户权利后再将其启用， 则在可以启用帐户之前，可能会有一些延迟。 SPO 和 Teams 会有 15 分钟的延迟。 对于 EXO，延迟为 35-40 分钟。

## <a name="faqs"></a>常见问题解答

### <a name="how-will-cae-work-with-sign-in-frequency"></a>如何将 CAE 与登录频率一起使用？

无论是否使用 CAE，登录频率都会得到遵循。

## <a name="next-steps"></a>后续步骤

[宣布连续访问评估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
