---
title: 配置 Azure 多重身份验证-Azure Active Directory
description: 本文介绍如何在 Azure 门户中配置 Azure 多重身份验证设置
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfde52db9cbe5bc14be7b30c1063215472bff699
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116282"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>配置 Azure 多重身份验证设置

本文介绍如何在 Azure 门户中管理多重身份验证设置。 本文涵盖了各种主题，可帮助你充分利用 Azure 多重身份验证。 并非所有版本的 Azure 多重身份验证都提供所有这些功能。

通过浏览到**Azure Active Directory**  >  **安全**  >  **MFA**，你可以从 Azure 门户访问与 Azure 多重身份验证相关的设置。

![Azure 门户 - Azure AD 多重身份验证设置](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>设置

这些设置中的一部分适用于 MFA 服务器和/或 Azure MFA。

| 功能 | 说明 |
| ------- | ----------- |
| 帐户锁定 | 如果连续有过多的身份验证尝试遭到拒绝，则将暂时在多重身份验证服务中锁定帐户。 此功能仅适用于输入 PIN 进行身份验证的用户。 （MFA 服务器） |
| [阻止/解除阻止用户](#block-and-unblock-users) | 用于阻止特定用户接收多重身份验证请求。 自动拒绝任何针对受阻止用户的身份验证尝试。 用户将在阻止日起 90 天内受到阻止。 |
| [欺诈警报](#fraud-alert) | 配置与用户报告欺诈验证请求相关的设置 |
| [通知](#notifications) | 允许通过 MFA 服务器来通知事件。 |
| [OATH 令牌](concept-authentication-methods.md#oath-hardware-tokens) | 用在基于云的 Azure MFA 环境中，为用户托管 OATH 令牌。 |
| [电话呼叫设置](#phone-call-settings) | 为云和本地环境配置与电话呼叫和问候语相关的设置。 |
| 提供程序 | 此项会显示任何现有的与帐户相关联的身份验证提供程序。 自 2018 年 9 月 1 日起，可能无法创建新的身份验证提供程序 |

## <a name="manage-mfa-server"></a>管理 MFA 服务器

此部分的设置仅适用于 MFA 服务器。

| 功能 | 说明 |
| ------- | ----------- |
| 服务器设置 | 下载 MFA 服务器并生成激活凭据，以便初始化环境 |
| [免验证一次](#one-time-bypass) | 在有限的时间内，允许用户在不执行双重验证的情况下进行身份验证。 |
| [缓存规则](#caching-rules) |  缓存主要用于本地系统（例如 VPN）在第一个请求进行中发送多个验证请求的情况。 使用此功能，当用户成功进行第一次验证后，后续请求会自动成功。 |
| 服务器状态 | 查看本地 MFA 服务器的状态，包括版本、状态、IP 以及上次通信时间和日期。 |

## <a name="activity-report"></a>活动报告

此处提供的报告特定于 MFA 服务器（本地）。 如需 Azure MFA（云）报告，请查看 Azure AD 中的登录报告。

## <a name="block-and-unblock-users"></a>阻止和解除阻止用户

使用阻止和解除阻止用户功能，可防止用户收到身份验证请求。__ 自动拒绝任何针对受阻止用户的身份验证尝试。 用户将在阻止日起 90 天内受到阻止。

### <a name="block-a-user"></a>阻止用户

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **阻止/解除阻止用户**。
3. 选择“添加”以阻止用户****。
4. 选择“复制组”****。 将被阻止的用户的用户名输入为**用户名 \@ domain.com**。 在“原因”字段输入注释****。
5. 选择“添加”，完成阻止用户操作****。

### <a name="unblock-a-user"></a>解除阻止用户

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **阻止/解除阻止用户**。
3. 在“操作”列中要解除阻止的用户旁，选择“解除阻止”********。
4. 在“解除阻止原因”字段输入注释****。
5. 选择“解除阻止”完成解除阻止用户操作****。

## <a name="fraud-alert"></a>欺诈警报

配置欺诈警报，以便用户举报企图访问其资源的欺诈行为。__ 用户可以通过移动应用或电话来举报欺诈行为。

### <a name="turn-on-fraud-alerts"></a>打开欺诈警报

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **欺诈警报**。
3. 将“允许用户提交欺诈警报”设置为“打开”。********
4. 选择“保存”  。

### <a name="configuration-options"></a>配置选项

* **报告欺诈时阻止用户** - 如果用户报告欺诈，那么他们的帐户将被阻止 90 天，或者直到管理员解除阻止他们的帐户。 管理员可以使用登录报表查看登录并采取相应的操作阻止将来的欺诈。 然后管理员可以[解除阻止](#unblock-a-user)用户帐户。
* **初始问候期间报告欺诈的代码** - 当用户接收电话呼叫来执行双重验证时，他们通常按 **#** 来确认其登录。 若要报告欺诈行为，用户需要在按下时输入代码 **#** 。 此代码默认为 **0**，但可以自定义此代码。

   >[!NOTE]
   >Microsoft 的默认问候语音将指示用户按 0# 以提交欺诈警报。**** 如果要使用 0 以外的代码，请录制并上传自定义语音问候，为用户提供相应指示。****
   >

### <a name="view-fraud-reports"></a>查看欺诈报告

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **登录**  >  **身份验证详细信息**。 欺诈报表现在是标准 Azure AD 登录报表的一部分，它将显示为 "MFA 已拒绝"、"欺诈代码已进入 **" 的 "结果详细信息"** 。
 
## <a name="notifications"></a>通知

在此处为将接收欺诈警报电子邮件**Azure Active Directory**  >  **安全**  >  **多重身份验证**  >  **通知**的用户配置电子邮件地址。

![通知欺诈警报电子邮件示例](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>电话呼叫设置

### <a name="caller-id"></a>呼叫方 ID

**MFA 呼叫方 ID 编号** - 这是用户在其电话上会看到的编号。 仅允许美国的编号。

>[!NOTE]
>通过公共电话网络拨打多重身份验证电话时，有时会通过不支持来电显示的运营商路由电话。 因此，尽管多重身份验证系统始终会发送呼叫号码，但并不保证会显示该号码。

在美国中，如果尚未配置 MFA 呼叫方 ID，来自 Microsoft 的语音呼叫来自以下号码： + 1 （866） 539 4191、+ 1 （855） 330 8653 和 + 1 （877） 668 6536。 如果使用垃圾邮件筛选器，请确保排除这些号码。

### <a name="custom-voice-messages"></a>自定义语音消息

可通过“自定义语音消息”功能，将自己的录音或问候语用于双重验证。__ 可以使用自定义语音消息增补或取代 Microsoft 录音。

在开始之前，请注意以下限制：

* 支持的文件格式为 .wav 和 .mp3。
* 文件大小限制为 1 MB。
* 身份验证消息应少于 20 秒。 超过 20 秒的消息可能导致验证失败。 用户可能不会在消息播放结束前作出响应，导致验证超时。

### <a name="custom-message-language-behavior"></a>自定义消息语言行为

当向用户播放自定义语音消息时，消息的语言取决于以下因素：

* 当前用户的语言。
  * 用户的浏览器检测到的语言。
  * 其他身份验证方案可能操作方式不同。
* 任何可用的自定义消息的语言。
  * 添加自定义消息时，由管理员选择此语言。

例如，如果只有一条采用德语的自定义消息：

* 以德语进行身份验证的用户将听到自定义德语消息。
* 以英语进行身份验证的用户将听到标准英语消息。

### <a name="set-up-a-custom-message"></a>设置自定义消息

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **电话呼叫设置**。
1. 选择“添加问候语”****。
1. 选择问候的类型。
1. 选择语言。
1. 选择要上传的 .mp3 或 .wav 声音文件。
1. 选择“添加”  。

### <a name="custom-voice-message-defaults"></a>自定义语音消息默认值

用于创建自定义消息的示例脚本。

| 消息名 | 脚本 |
| --- | --- |
| 身份验证成功 | 已成功验证你的登录。 不用. |
| 扩展提示 | 感谢您使用 Microsoft 的登录验证系统。 请按井号键继续。 |
| 欺诈确认 | 已提交欺诈警报。 若要解除阻止你的帐户，请联系你公司的 IT 支持人员。 |
| 欺诈问候语（标准） | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 如果未启动此验证，则可能有人尝试访问你的帐户。 请按零号以提交欺诈警报。 这会通知您公司的 IT 团队，并阻止进一步的验证尝试。 |
| 已提交欺诈警报。 | 若要解除阻止你的帐户，请联系你公司的 IT 支持人员。 |
| 激活 | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 |
| 已拒绝身份验证重试 | 验证被拒绝。 |
| 重试（标准） | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 |
| 问候语（标准） | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 |
| 问候语（PIN） | 感谢您使用 Microsoft 的登录验证系统。 请输入你的 PIN，后跟井号键来完成验证。 |
| 欺诈问候语（PIN） | 感谢您使用 Microsoft 的登录验证系统。  请输入你的 PIN，后跟井号键来完成验证。 如果未启动此验证，则可能有人尝试访问你的帐户。 请按零号以提交欺诈警报。 这会通知您公司的 IT 团队，并阻止进一步的验证尝试。 |
| 重试（PIN） | 感谢您使用 Microsoft 的登录验证系统。 请输入你的 PIN，后跟井号键来完成验证。 |
| 数字后的扩展提示 | 如果已在此扩展中，请按井号键继续。 |
| 身份验证被拒绝 | 很抱歉，我们此时无法登录。 请稍后重试。 |
| 激活问候语（标准） | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 |
| 激活重试（标准） | 感谢您使用 Microsoft 的登录验证系统。 请按井号键完成验证。 |
| 激活问候语（PIN） | 感谢您使用 Microsoft 的登录验证系统。 请输入你的 PIN，后跟井号键来完成验证。 |
| 数字之前的扩展提示 | 感谢您使用 Microsoft 的登录验证系统。 请将此调用转移到扩展 .。。 |

## <a name="one-time-bypass"></a>免验证一次

“免验证一次”功能允许用户在不执行双重验证的情况下通过一次身份验证。__ 免验证是暂时性的，会在指定的秒数后过期。 在移动应用或电话无法接收通知或来电的情况下，可以启用“免验证一次”，使用户能够访问所需的资源。

### <a name="create-a-one-time-bypass"></a>创建免验证一次

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **一次性跳过**。
3. 选择“添加”  。
4. 如有必要，可选择要免验证的复制组。
5. 输入用户名 domain.com 作为**用户名 \@ **。 输入免验证应持续的秒数。 输入免验证的原因。
6. 选择“添加”  。 时间限制将立即生效。 用户需在免验证一次过期前登录。

### <a name="view-the-one-time-bypass-report"></a>查看免验证一次报告

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **一次性跳过**。

## <a name="caching-rules"></a>缓存规则

可设置用户使用“缓存”__ 功能进行身份验证后允许身份验证尝试的时间段。 在指定时间段内，用户的后续身份验证尝试将自动成功。 缓存主要用于本地系统（例如 VPN）在第一个请求进行中发送多个验证请求的情况。 使用此功能，当用户成功进行第一次验证后，后续请求会自动成功。

>[!NOTE]
>缓存功能不应用于登录到 Azure Active Directory (Azure AD)。

### <a name="set-up-caching"></a>设置缓存

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **缓存规则**。
3. 选择“添加”  。
4. 从下拉列表中选择缓存类型****。 输入最大缓存秒数****。
5. 如有必要，可选择身份验证类型并指定应用程序。
6. 选择“添加”  。

## <a name="mfa-service-settings"></a>MFA 服务设置

Azure 多重身份验证的应用密码、受信任 IP、验证选项以及“记住多重身份验证”设置可以在服务设置中找到。 可以通过浏览到**Azure Active Directory**  >  **安全**  >  **MFA**  >  **入门**  >  **配置**  >  **其他基于云的 mfa 设置**，从 Azure 门户访问服务设置。

![Azure 多重身份验证服务设置](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

受信任的 IP 地址范围可以是专用或公用的。

## <a name="app-passwords"></a>应用密码

某些应用程序（例如 Office 2010 或更低版本，以及 iOS 11 之前的 Apple Mail）不支持双重验证。 这些应用未配置为接受第二个验证。 若要使用这些应用程序，请利用“应用密码”__ 功能。 可使用应用密码代替传统密码，允许应用绕过双重验证并继续运行。

Microsoft Office 2013 客户端及更高版本支持现代身份验证。 Office 2013 客户端（包括 Outlook）支持现代身份验证协议，并且可以为其启用双重验证。 启用客户端后，客户端即不要求使用应用密码。

>[!NOTE]
>应用密码不适用于基于多重身份验证策略和新式身份验证的条件性访问。

### <a name="considerations-about-app-passwords"></a>有关应用密码的注意事项

使用应用密码时，请考虑以下重要事项：

* 每个应用程序仅要求输入一次应用密码。 用户不需要记录这些密码，也不用每次都输入它们。
* 实际的密码会自动生成，并非由用户提供。 自动生成的密码会使攻击者更难进行猜测，因而更安全。
* 目前，每个用户的密码限制为 40 个。
* 缓存密码并在本地使用它们的应用程序可能开始失败，因为应用密码在工作或学校帐户外并非已知。 这种情况的一个例子是 Exchange 电子邮件位于本地而存档邮件位于云中。 在这种情况下，不能使用同一密码。
* 对用户帐户启用多重身份验证后，应用密码就可用于大多数非浏览器客户端，例如 Outlook 和 Microsoft Skype for Business。 不能通过非浏览器应用程序（如 Windows PowerShell）使用应用密码执行管理操作。 即使用户具有管理帐户，也无法执行这些操作。 若要运行 PowerShell 交吧，应创建具有强密码的服务帐户，并且不为该帐户启用双重验证。

>[!WARNING]
>在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 需要域密码才能进行本地身份验证。 需要应用密码才能进行云身份验证。

### <a name="guidance-for-app-password-names"></a>应用密码命名指南

应用密码名称应能反映使用它们的设备。 如果有安装了非浏览器应用（如 Outlook、Word 和 Excel）的笔记本电脑，可为这些应用创建名为 Laptop 的应用密码。**** 为在台式计算机上运行的相同应用程序创建名为 Desktop 的另一个应用密码。****

>[!NOTE]
>建议为每个设备（而不是每个应用程序）创建一个应用密码。

### <a name="federated-or-single-sign-on-app-passwords"></a>联合或单一登录应用密码

Azure AD 支持通过联合或单一登录使用本地 Windows Server Active Directory 域服务 (AD DS)。 如果组织与 Azure AD 联合且使用 Azure 多重身份验证，请考虑以下应用密码相关要点。

>[!NOTE]
>以下要点仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，不会与标识提供者 (IdP) 联系。 应用密码存储在工作或学校帐户中。 如果用户离开公司，则该用户的信息通过 **DirSync** 实时流向工作或学校帐户。 禁用/删除帐户可能需要至多三小时才能同步，这可能使 Azure AD 中的应用密码禁用/删除出现延迟。
* 应用密码功能不遵循“本地客户端访问控制”设置。
* 没有可与应用密码功能同时使用的本地身份验证日志记录/审核功能。
* 某些高级体系结构要求使用组合凭据进行客户端双重验证。 凭据可以包括工作或学校帐户的用户名和密码以及应用密码。 要求取决于如何执行身份验证。 对于针对本地基础结构进行身份验证的客户端，需要使用工作或学校帐户用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，需要使用应用密码。

  例如，假设有以下体系结构：

  * Active Directory 的本地实例与 Azure AD 联合。
  * 使用 Exchange Online。
  * 本地使用 Skype for Business。
  * 使用 Azure 多重身份验证。

  在此方案中，使用以下凭据：

  * 若要登录到 Skype for Business，请使用工作或学校帐户的用户名和密码。
  * 若要从连接到 Exchange Online 的 Outlook 客户端访问通讯簿，请使用应用密码。

### <a name="allow-users-to-create-app-passwords"></a>允许用户创建应用密码

默认情况下，用户无法创建应用密码。 必须启用应用密码功能。 若要允许用户创建应用密码，请使用以下过程：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **用户**"。
3. 选择“多重身份验证”  。
4. 在“多重身份验证”下，选择“服务设置”。****
5. 在“服务设置”页上，选择“允许用户创建应用密码，以登录非浏览器应用”选项。********

### <a name="create-app-passwords"></a>创建应用密码

用户可以在最初注册时创建应用密码。 用户可在注册过程结束时选择创建应用密码。

用户还可以在注册后创建应用密码。 有关适用于用户的详细信息和详细步骤，请参阅 [Azure 多重身份验证中的应用密码是什么？](../user-help/multi-factor-authentication-end-user-app-passwords.md)。

## <a name="trusted-ips"></a>受信任的 IP

托管或联合租户的管理员可使用 Azure 多重身份验证的“受信任 IP”__ 功能。 此功能将为从公司 Intranet 登录的用户绕过双重验证。 Azure 多重身份验证的完整版本中为管理员提供了此功能，免费版本中没有提供。 有关如何获取 Azure 多重身份验证的完整版本的详细信息，请参阅 [Azure 多重身份验证](multi-factor-authentication.md)。

> [!NOTE]
> MFA 受信任的 Ip 和命名位置的条件访问仅适用于 IPV4 地址。

如果你的组织部署 NPS 扩展，向本地应用程序提供 MFA，请注意源 IP 地址将始终显示为身份验证尝试流经的 NPS 服务器。

| Azure AD 租户类型 | 受信任的 IP 功能选项 |
|:--- |:--- |
| 托管 |**特定 IP 地址范围**：由管理员指定一组 IP 地址范围，以便为从公司 Intranet 登录的用户绕过双重验证。 最多可以配置50个受信任的 IP 范围。|
| 联合 |**所有联合用户**：从组织内部登录的所有联合用户都可绕过双重验证。 用户使用由 Active Directory 联合身份验证服务 (AD FS) 颁发的声明绕过验证。<br/>**特定 IP 地址范围**：由管理员指定一组 IP 地址范围，以便为从公司 Intranet 登录的用户绕过双重验证。 |

绕过受信任的 IP 仅在公司 Intranet 内有效。 如果选择“所有联合用户”选项，则用户从公司 Intranet 外部登录时，必须使用双重验证进行身份验证****。 即使用户提供了 AD FS 声明，验证过程也相同。 

### <a name="end-user-experience-inside-of-corpnet"></a>公司网络内的最终用户体验

如果禁用了“受信任的 IP”功能，则浏览器通信流需要进行双重验证。 较旧的丰富客户端应用程序需要应用密码。

如果启用了“受信任的 IP”功能，则浏览器通信流无需进行双重验证**。 如果用户未创建应用密码，则较旧的丰富客户端应用程序要求提供应用密码**。 使用应用密码后，将始终要求提供该密码。 

### <a name="end-user-experience-outside-corpnet"></a>公司网络外的最终用户体验

无论是否启用“受信任的 IP”功能，浏览器通信流都需要进行双重验证。 较旧的丰富客户端应用程序需要应用密码。

### <a name="enable-named-locations-by-using-conditional-access"></a>通过使用条件访问启用命名位置

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **安全**  >  **条件性访问**  >  **命名位置**"。
3. 选择“新建位置”。****
4. 输入位置的名称。
5. 选择 "**标记为受信任位置**"。
6. 以 CIDR 表示法输入 IP 范围（例如 192.168.1.1/24）。****
7. 选择“创建”  。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>通过使用条件访问启用受信任的 Ip 功能

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **安全**  >   **条件性访问**  >  **命名位置**"。
3. 选择 "**配置 MFA 受信任的 ip**"。
4. 在“服务设置”页的“受信任的 IP”下，从以下两个选项中进行选择********：

   * **对于联合用户从我的 Intranet 发起的请求**：选中此复选框以选择此选项。 所有从公司网络登录的联合用户均可使用 AD FS 发布的声明免除双重验证。 请确保 AD FS 具有可将 intranet 声明添加到相应流量的规则。 如果规则不存在，请在 AD FS 中创建以下规则：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **对于来自特定公共 IP 范围的请求**：使用 CIDR 表示法在文本框中输入 IP 地址，以选择此选项。
      * 若要选择 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 范围内的 IP 地址，请使用如 **xxx.xxx.xxx.0/24** 的表示法。
      * 对于单个 IP 地址，请使用类似于**xxx.xxx.xxx.xxx/32**的表示法。
      * 可输入至多 50 个 IP 地址范围。 从这些 IP 地址登录的用户将免除双重验证。

5. 选择“保存”  。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>使用服务设置启用受信任的 IP 功能

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **用户**"。
3. 选择“多重身份验证”  。
4. 在“多重身份验证”下，选择“服务设置”。****
5. 在“服务设置”页的“受信任的 IP”下，选择以下两个选项中的一个（或两个）********：

   * **对于来自我的 Intranet 的联合用户请求**：选中此复选框以选择此选项。 所有从公司网络登录的联合用户均可使用 AD FS 发布的声明免除双重验证。 请确保 AD FS 具有可将 intranet 声明添加到相应流量的规则。 如果规则不存在，请在 AD FS 中创建以下规则：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **对于来自特定 IP 地址子网范围的请求**：使用 CIDR 表示法在文本框中输入 IP 地址，以选择此选项。
      * 若要选择 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 范围内的 IP 地址，请使用如 **xxx.xxx.xxx.0/24** 的表示法。
      * 对于单个 IP 地址，请使用类似于**xxx.xxx.xxx.xxx/32**的表示法。
      * 可输入至多 50 个 IP 地址范围。 从这些 IP 地址登录的用户将免除双重验证。

6. 选择“保存”  。

## <a name="verification-methods"></a>验证方法

可以选择可供用户使用的验证方法。 下表提供了这些方法的简要概述。

用户为其帐户注册 Azure 多重身份验证时，可从你启用的选项中选择其首选验证方法。 [为我的帐户设置双重验证帐户](../user-help/multi-factor-authentication-end-user-first-time.md)中提供了用户注册过程指导。

| 方法 | 说明 |
|:--- |:--- |
| 拨打电话 |拨打自动语音电话。 用户接听电话，并按电话键盘上的 # 进行身份验证。 此电话号码不会同步到本地 Active Directory。 |
| 向手机发送短信 |发送包含验证码的短信。 系统会提示用户在登录界面中输入验证代码。 此过程称为单向短信。 双向短信意味着用户必须短信回复一个特定代码。 已弃用双向短信，2018 年 11 月 14 日后不再受到支持。 管理员应该为以前使用双向短信的用户启用其他方法。|
| 通过移动应用发送通知 |向手机或已注册设备发送推送通知。 用户将查看通知并选择**验证**来完成验证。 Microsoft Authenticator 应用可用于 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 移动应用或硬件标志提供的验证码 |Microsoft Authenticator 应用每隔 30 秒会生成一个新的 OATH 验证码。 用户将此验证码输入到登录界面中。 Microsoft Authenticator 应用可用于 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="enable-and-disable-verification-methods"></a>启用和禁用可选择验证方法

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **用户**"。
3. 选择“多重身份验证”  。
4. 在“多重身份验证”下，选择“服务设置”。****
5. 在“服务设置”页上的“验证选项”**** 下，选择/取消选择要向用户提供的方法。****
6. 单击“保存”  。

若要更详细地了解如何使用身份验证方法，可参阅[有哪些身份验证方法](concept-authentication-methods.md)一文。

## <a name="remember-multi-factor-authentication"></a>记住多重身份验证

多重身份验证用户可免费对其信任的设备和浏览器使用“记住多重身份验证”功能。__ 用户使用多重身份验证成功登录到设备后，可在指定天数内跳过后续验证。 此功能可以尽量减少用户须在同一设备上执行双重验证的次数，从而可以提高可用性。

>[!IMPORTANT]
>请记住，如果帐户或设备遭到入侵，受信任的设备的多重身份验证可能影响安全性。 如果企业帐户遭到入侵，或者受信任的设备丢失或被盗，则应[吊销 MFA 会话](howto-mfa-userdevicesettings.md)。
>
>还原操作会撤销所有设备的受信任状态，需要用户重新执行双重验证。 还可以指示用户按照[管理双重验证设置](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)中的说明在其自己的设备上还原多重身份验证。

### <a name="how-the-feature-works"></a>该功能的工作原理

在用户在登录时选择“X 天内不再询问”选项时，“记住多重身份验证”功能将在浏览器上设置持久性 Cookie。**** 在该 Cookie 过期之前，该浏览器不会再次提示用户进行多重身份验证。 如果用户在同一设备上打开不同浏览器或清除其 Cookie，会提示他们重新进行验证。

非浏览器应用程序上不会显示“X 天内不再询问”**** 选项，无论该应用是否支持新式身份验证。 这些应用使用每小时提供新访问令牌的刷新令牌。__ 验证刷新令牌时，Azure AD 会检查上次双重验证是否发生在指定的天数内。

此功能可以减少 Web 应用的身份验证次数，而通常每次使用时都会提示验证。 此功能会增加新式身份验证客户端的身份验证次数（通常每隔 90 天提示一次）。 与条件访问策略结合使用时，还可能增加身份验证的数量。

>[!IMPORTANT]
>当用户通过 Azure 多重身份验证服务器或第三方多重身份验证解决方案对 AD FS 执行双重验证时，“记住多重身份验证”**** 功能与 AD FS 的“使我保持登录”**** 功能不兼容。
>
>如果你的用户在 AD FS 中选择“使我保持登录”**** 同时将其设备标记为信任多重身份验证，则“记住多重身份验证”**** 天数过期后不会自动验证该用户。 Azure AD 将请求全新双重验证，但 AD FS 将返回包含原始多重身份验证声明和日期的令牌，而不是重新执行双重验证。 **此反应会引发 Azure AD 和 AD FS 之间的验证循环。**
>
>"**记住多重身份验证**" 功能与 b2b 用户不兼容，在登录到受邀的租户时，b2b 用户不会看到此功能。
>

### <a name="enable-remember-multi-factor-authentication"></a>启用“记住多重身份验证”

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择 " **Azure Active Directory**  >  **用户**"。
3. 选择“多重身份验证”  。
4. 在“多重身份验证”下，选择“服务设置”。****
5. 在“服务设置”页的“管理记住多重身份验证设置”下，选择“允许用户在其信任的设备上记住多重身份验证”************ 选项。
6. 设置允许受信任设备免除双重验证的天数。 默认值为 14 天。
7. 选择“保存”  。

### <a name="mark-a-device-as-trusted"></a>将设备标记为受信任的

启用“记住多重身份验证”功能后，用户可在登录时选择“不再询问”****，将设备标记为信任。

## <a name="next-steps"></a>后续步骤

[修改 Azure AD 登录页品牌打造](../fundamentals/customize-branding.md)
