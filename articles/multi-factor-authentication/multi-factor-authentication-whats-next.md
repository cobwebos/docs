---
title: "配置 Azure MFA | Microsoft 文档"
description: "这个有关 Azure 多重身份验证的页面介绍了使用 MFA 可以执行的后续步骤。  其中包括报告、欺诈警报、一次性跳过、自定义语音消息、缓存，受信任的 IP 和应用密码。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4900707baa875ae4527d82e8189d5bc4d319ae0c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-multi-factor-authentication-settings---public-preview"></a>配置 Azure 多重身份验证设置 - 公共预览版

在启动并运行 Azure 多重身份验证后，可以参考本文进行管理。  本文涵盖了各种主题，可帮助你充分利用 Azure 多重身份验证。  并非每个 [Azure 多重身份验证版本](/multi-factor-authentication-get-started.md#what-features-do-i-need)都提供了所有这些功能。

>[!NOTE]
>这些设置在 Azure 门户中处于公共预览版阶段。 有关如何在 pfweb 门户中管理 Azure 多重身份验证设置的文档，请参阅[配置 Azure 多重身份验证设置](multi-factor-authentication-whats-next-pfweb.md)。

| 功能 | 说明 | 
|:--- |:--- |
| [阻止和解除阻止用户](#block-and-unblock) |阻止/解除阻止用户可防止用户收到身份验证请求。 |
| [欺诈警报](#fraud-alert) |管理员可以配置和设置欺诈警报，以便用户针对访问其资源的欺诈性企图进行举报。 |
| [免验证一次](#one-time-bypass) |“一次性跳过”可让用户通过“跳过”多重身份验证来进行身份验证，不过只能跳过一次。 |
| [自定义语音消息](#custom-voice-messages) |自定义语音消息可让你将自己的录音或问候语用于多重身份验证。 |
| [缓存](#caching-in-azure-multi-factor-authentication) |使用缓存可以设置一段特定的时间，使后续的身份验证尝试自动成功。 |
| [受信任的 IP](#trusted-ips) |托管或联合租户的管理员可以使用“受信任的 IP”对从公司本地 Intranet 登录的用户免除双重验证。 |
| [应用密码](#app-passwords) |应用密码允许非 MFA 感知应用程序免于进行多重身份验证并继续工作。 |
| [为已记住设备和浏览器记住多重身份验证](#remember-multi-factor-authentication-for-devices-that-users-trust) |允许在用户使用 MFA 成功登录后的指定天数内记住设备。 |
| [可选择验证方法](#selectable-verification-methods) |允许选择可供用户使用的身份验证方法。 |

## <a name="block-and-unblock"></a>阻止和解除阻止
阻止/解除阻止用户可用于防止用户收到身份验证请求。 将自动拒绝任何针对受阻止用户的身份验证尝试。 自受阻日起，受阻止用户在 90 天内会受到阻止。

### <a name="block-a-user"></a>阻止用户
1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “阻止/解除阻止用户”。
3. 单击“添加”以阻止某个用户。
4. 选择“复制组”，输入 username@domain.com 作为受阻止用户名，并在“原因”字段输入注释。
5. 单击“添加”，完成阻止用户操作。

### <a name="unblock-a-user"></a>解除阻止用户
1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “阻止/解除阻止用户”。
3. 单击“操作”列中要解除阻止的用户旁的“解除阻止”。
4. 在“解除阻止原因”字段输入注释。
5. 单击“解除阻止”完成解除阻止用户操作。

## <a name="fraud-alert"></a>欺诈警报
管理员可以配置和设置欺诈警报，以便用户针对访问其资源的欺诈性企图进行举报。  用户可以通过移动应用或电话来报告欺诈行为。

### <a name="turn-on-fraud-alert"></a>打开欺诈警报
1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “欺诈警报”。

   ![欺诈警报](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. 将“允许用户提交欺诈警报”设置为“打开”。
4. 选择“保存”。

### <a name="configuration-options"></a>配置选项

- **报告欺诈时阻止用户** - 如果用户报告欺诈，那么他们的帐户将被阻止 90 天，或者直到管理员解除阻止他们的帐户。 管理员可以使用登录报表查看登录并采取相应的操作阻止将来的欺诈。 然后管理员可以[解除阻止](#unblock-a-user)用户帐户。
- **初始问候期间报告欺诈的代码** - 当用户接收电话呼叫来执行双重验证时，他们通常按 # 来确认其登录。 如果他们想要报告欺诈，则可在按 # 之前输入代码。 此代码默认为 **0**，但可以自定义此代码。

> [!NOTE]
> Microsoft 的默认问候语音将指示用户按 0# 以提交欺诈警报。 如果要使用 0 以外的代码，应根据相应说明记录并上传自己的自定义问候语音。

### <a name="view-fraud-reports"></a>查看欺诈报告
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 在“服务设置”页面底部，选择“转到门户”。
7. 在 Azure 多重身份验证管理门户中，单击“查看报告”下的“欺诈警报”。
8. 指定要在报告中查看的日期范围。 还可以指定用户名、电话号码和用户状态。
9. 单击“运行”。 这会显示欺诈警报的报告。 如果要导出报告，请单击“导出到 CSV”。

## <a name="one-time-bypass"></a>免验证一次
“免验证一次”允许用户在不执行双重验证的情况下进行身份验证，不过只会免除一次。 免验证是暂时性的，会在指定的秒数后过期。 在移动应用或电话无法接收通知或来电的情况下，可以启用“免验证一次”，使用户能够访问所需的资源。

### <a name="create-a-one-time-bypass"></a>创建免验证一次

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “免验证一次”。

   ![免验证一次](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. 选择“添加”。
4. 如果有必要，请选择此免验证的复制组。
5. 输入用户名（采用 username@domain.com 形式）、免验证将存在的秒数，以及免验证的原因。 
6. 选择“添加”。 时间限制将立即生效，因此，用户需要在免验证一次过期前登录。 

### <a name="view-the-one-time-bypass-report"></a>查看免验证一次报告
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 在“服务设置”页面底部，选择“转到门户”。
7. 在 Azure 多重身份验证管理门户中，在“查看报告”下，单击“免验证一次”。
8. 指定要在报告中查看的日期范围。 还可以指定用户名、电话号码和用户状态。
9. 单击“运行”。 这会显示免验证报告。 如果要导出报告，请单击“导出到 CSV”。

## <a name="custom-voice-messages"></a>自定义语音消息
“自定义语音消息”允许将自己的录音或问候语用于双重验证。 可以在 Microsoft 录音之外额外使用自定义语音消息，或使用它来取代 Microsoft 录音。

在开始之前，请注意以下事项：

* 支持的文件格式为 .wav 和 .mp3。
* 文件大小上限是 5MB。
* 身份验证消息应少于 20 秒。 大于该长度的消息可能会导致验证失败，因为用户可能无法在消息完成前做出响应，从而导致验证超时。

### <a name="set-up-a-custom-message"></a>设置自定义消息

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “电话呼叫设置”。

   ![电话呼叫设置](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. 选择“添加问候语”。
4. 选择问候语类型和语言。
5. 选择要上传的 .mp3 或 .wav 声音文件。
6. 选择“添加”。

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的缓存
使用缓存可以设置一个特定的时间段，使该时间段内的后续身份验证尝试自动成功。 缓存主要应用场合是当第一个请求仍在进行时，本地系统（例如 VPN）发送多个验证请求。 这样，在用户成功进行第一次验证后，后续请求会自动成功完成。 

到 Azure AD 的登录并未打算使用缓存。

### <a name="set-up-caching"></a>设置缓存 
1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “MFA 服务器” > “缓存规则”。

   ![缓存规则](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. 选择“添加”。
5. 从下拉列表中选择缓存类型，并指定最大缓存秒数。 
6. 如有必要，选择一种身份验证类型，并指定一个应用程序。 
7. 选择“添加”。


## <a name="trusted-ips"></a>受信任的 IP
“受信任的 IP”是 Azure MFA 的一项功能，托管或联合租户的管理员可以使用此功能对从公司本地 Intranet 登录的用户免除双重验证。 Azure 多重身份验证的完整版本中为管理员提供了此功能，免费版本中没有提供。 有关如何获取 Azure 多重身份验证的完整版本的详细信息，请参阅 [Azure 多重身份验证](multi-factor-authentication.md)。

| AZURE AD 租户类型	 | 可用的受信任 IP 选项 |
|:--- |:--- |
| 托管 |<li>特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可免除双重验证的 IP 地址范围。</li> |
| 联合 |<li>所有联合用户 - 所有从组织内部登录的联合用户都将使用 AD FS 发布的声明免除双重验证。</li><br><li>特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可免除双重验证的 IP 地址范围。 |

这种跳过只能从公司 Intranet 进行。 例如，如果选择了“所有联合用户”，当用户从公司 Intranet 外部登录时，即使该用户出示 AD FS 声明，也必须使用双重验证进行身份验证。 

**公司网络内的最终用户体验：**

当禁用了“受信任的 IP”时，浏览器流要求进行双重验证，较旧的丰富客户端应用要求提供应用密码。 

当启用了“受信任的 IP”时，如果用户尚未创建应用密码，则浏览器流*不*要求进行双重验证，较旧的丰富客户端应用*不*要求提供应用密码。 一旦使用了应用密码，则始终会要求提供该密码。 

**公司网络外的最终用户体验：**

无论是否启用了“受信任的 IP”，浏览器流都要求进行双重验证，较旧的丰富客户端应用都要求提供应用密码。 

### <a name="to-enable-trusted-ips"></a>启用受信任的 IP
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 在“服务设置”页上，在“受信任的 IP”下有两个选项：
   
   * **对于联合用户从我的 Intranet 发起的请求** – 选中此框。 所有从公司网络登录的联合用户都将使用 AD FS 发布的声明免除双重验证。 请确保 AD FS 具有可将 intranet 声明添加到相应流量的规则。 如果该规则尚不存在，应在 AD FS 中创建以下规则：“c:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);”



   * **对于来自特定公共 IP 范围的请求** – 使用 CIDR 表示法在提供的文本框中输入 IP 地址。 例如：xxx.xxx.xxx.0/24 表示 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 范围内的 IP 地址；xxx.xxx.xxx.xxx/32 表示单个 IP 地址。 最多可以输入 50 个 IP 地址范围。 从这些 IP 地址登录的用户将免除双重验证。
7. 单击“保存” 。
8. 应用更新后，单击“关闭”。

![受信任的 IP](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>应用密码
某些应用（例如 Office 2010 或更低版本和 Apple Mail）不支持双重验证。 它们未配置为接受第二个验证。 要使用这些应用，需要使用“应用密码”来替换传统密码。 应用密码可让应用程序免除双重验证并继续工作。

> [!NOTE]
> 适用于 Office 2013 客户端的现代身份验证
> 
> Office 2013 客户端（包括 Outlook）支持现代身份验证协议，并且可以为其启用双重验证。 在启用后，这些客户端不要求提供应用密码。  有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

### <a name="important-things-to-know-about-app-passwords"></a>有关应用密码的重要事项
下面是你应该知道的有关应用密码的重要事项列表。

* 对于每个应用，应当只需要为其输入一次应用密码。 用户不需要记录这些密码并且每次都输入它们。
* 实际的密码会自动生成，并非由用户提供。 这是因为自动生成的密码会使攻击者更难进行猜测，因而更安全。
* 目前，每个用户的密码限制为 40 个。 
* 缓存密码并在本地方案中使用该密码的应用可能启动失败，因为超出组织 ID 范畴的应用密码不可识别。例如 Exchange 电子邮件，这些电子邮件位于本地但存档邮件位于云中。 无法使用同一密码。
* 对用户的帐户启用多重身份验证后，可以在大多数非浏览器客户端（例如 Outlook 和 Lync）上使用应用密码，但无法通过 Windows PowerShell 等非浏览器应用程序使用应用密码执行管理操作，即使该用户具有管理帐户。  请确保使用强密码创建服务帐户以运行 PowerShell 脚本，而不要为该帐户启用双重验证。

> [!WARNING]
> 在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 这是因为本地身份验证需要域密码，而云身份验证需要应用密码。

### <a name="naming-guidance-for-app-passwords"></a>应用密码命名指南
应用密码名称应能反映使用它们的设备。 例如，如果有一台安装了非浏览器应用（如 Outlook、Word 和 Excel）的便携式计算机，则创建一个名为 Laptop 的应用密码，并在这些应用程序中使用该应用密码。 然后，为桌面计算机上的相同应用程序创建名为 Desktop 的另一个应用密码。 

Microsoft 建议为每个设备创建一个应用密码，而不是为每个应用程序创建一个应用密码。

### <a name="federated-sso-app-passwords"></a>联合 (SSO) 应用密码
Azure AD 支持与本地 Windows Server Active Directory 域服务 (AD DS) 的联合（单一登录）。 如果组织与 Azure AD 联合 (SSO) 并且要使用 Azure 多重身份验证，则关于应用密码的以下信息对你很重要。 本部分仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，我们从不转到标识提供者 (IdP)。 密码将存储在组织 ID 中。如果用户离开公司，则该信息必须通过 DirSync 实时流到组织 ID 中。 帐户禁用/删除可能需要长达三个小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵循“本地客户端访问控制”设置。
* 没有为应用密码提供本地身份验证日志记录/审核功能。
* 在对客户端使用双重验证时，某些先进的体系结构设计可能要求将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。 对于针对本地基础结构进行身份验证的客户端，会使用组织用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，会使用应用密码。

  例如，假设有一个由下列内容组成的体系结构：

  * 要将 Active Directory 的本地实例与 Azure AD 联合
  * 要使用 Exchange Online
  * 要使用本地专用的 Lync
  * 要使用 Azure 多重身份验证

  ![验证](./media/multi-factor-authentication-whats-next/federated.png)

  在这些情况下，必须执行以下操作：

  * 登录到 Lync 时，使用组织的用户名和密码。
  * 当尝试通过连接到 Exchange Online 的 Outlook 客户端访问通讯簿时，请使用应用密码。

### <a name="allow-app-password-creation"></a>允许创建应用密码
默认情况下，用户无法创建应用密码。 为此，必须启用此功能。 若要允许用户创建应用密码，请使用以下过程：

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 选中“允许用户创建用于登录到非浏览器应用的应用密码”旁边的单选按钮。

![创建应用密码](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>创建应用密码
用户可以在最初注册时创建应用密码。 注册过程结束时，系统将提供一个用户可用来创建应用密码的选项。

在注册后，用户还可以通过在 Azure 门户或 Office 365 门户中更改设置来创建应用密码。 有关适用于用户的详细信息和详细步骤，请参阅[什么是 Azure 多重身份验证中的应用密码](./end-user/multi-factor-authentication-end-user-app-passwords.md)。

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>记住用户信任的设备的多重身份验证
记住用户信任的设备和浏览器的多重身份验证是针对所有 MFA 用户的一项免费功能。 此功能允许在用户使用 MFA 执行成功登录后的指定天数内为用户提供绕过 MFA 的选项。 这可以尽量减少用户可能在同一设备上执行双重验证的次数，从而可以提高可用性。

但是，如果帐户或设备遭到入侵，则记住信任的设备的 MFA 可能会影响安全性。 如果公司帐户受到安全威胁，或者受信任的设备已丢失或被盗，则应[在所有设备上还原多重身份验证](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user)。 此操作会撤销所有设备的受信任状态，需要用户重新执行双重验证。 还可以指示用户按照[管理双重验证设置](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)中的说明在其自己的设备上还原 MFA

### <a name="how-it-works"></a>工作原理

在用户在登录时选中“**X** 天不再询问”框的情况下，通过在浏览器上设置持久性 Cookie，记住多重身份验证的有效性。 在 Cookie 过期之前，不会提示用户再次从该浏览器进行 MFA。 如果用户在同一设备上打开不同浏览器或清除其 Cookie，会提示他们重新进行验证。 

非浏览器应用不会显示“**X** 天不再询问”复选框，无论它们是否支持新式身份验证。 这些应用使用每小时提供新访问令牌的刷新令牌。 验证刷新令牌时，Azure AD 会检查最后一次执行双重验证的时间是否在配置的天数内。 

因此，在受信任的设备上记住 MFA 会减少在 Web 应用上进行身份验证的次数（通常每次都提示），但会增加新式身份验证客户端的身份验证次数（通常每隔 90 天提示一次）。

> [!NOTE]
>当用户通过 Azure MFA 服务器或第三方 MFA 解决方案向 AD FS 执行双重验证时，此功能与 AD FS 的“使我保持登录”功能不兼容。 如果用户在 AD FS 上选择“使我保持登录”，并且还将其设备标记为“受信任，可以跳过 MFA”，则在“记住 MFA”天数期满后，他们将不能验证。 Azure AD 将请求全新双重验证，但 AD FS 将返回包含原始 MFA 声明和日期的令牌，而不是重新执行双重验证。 这会引发 Azure AD 和 AD FS 之间的验证循环。 

### <a name="enable-remember-multi-factor-authentication"></a>启用“记住多重身份验证”
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 在“服务设置”页上，在“管理用户设备设置”下，选中“允许用户在其信任的设备上记住多重身份验证”复选框。
   ![记住设备](./media/multi-factor-authentication-whats-next/remember.png)
7. 设置希望允许受信任设备免除双重验证的天数。 默认值为 14 天。
8. 单击“保存” 。
9. 单击“**关闭**”。

### <a name="mark-a-device-as-trusted"></a>将设备标记为受信任的

启用此功能后，用户在登录时可以通过选中**不再询问**将设备标记为受信任的。

![不再询问 - 屏幕截图](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>可选择验证方法
可以选择哪些验证方法可供用户使用。 下表提供每个方法的简要概述。

当用户注册 MFA 帐户时，可以选择已启用选项之外的首选验证方法。 [设置我的双重验证帐户](multi-factor-authentication-end-user-first-time.md)中介绍了注册过程指导

| 方法 | 说明 |
|:--- |:--- |
| 拨打电话 |拨打自动语音电话。 用户接听电话，并按电话键盘上的 # 进行身份验证。 此电话号码不会同步到本地 Active Directory。 |
| 向手机发送短信 |发送包含验证码的短信。 系统会提示用户使用验证码回复短信或在登录界面中输入验证码。 |
| 通过移动应用发送通知 |向手机或已注册设备发送推送通知。 用户将查看通知并选择**验证**来完成验证。 <br>Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 通过移动应用发送验证码 |Microsoft Authenticator 应用每隔三十秒会生成一个新的 OATH 验证码。 用户将此验证码输入到登录界面中。<br>Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="how-to-enabledisable-authentication-methods"></a>如何启用/禁用身份验证方法
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择要管理的目录。 
4. 选择“配置”
5. 在“多重身份验证”下，选择“管理服务设置”。
6. 在“服务设置”页上的验证选项下，选择/取消选择要使用的选项。
   ![验证选项](./media/multi-factor-authentication-whats-next/authmethods.png)
7. 单击“保存” 。
8. 单击“**关闭**”。

