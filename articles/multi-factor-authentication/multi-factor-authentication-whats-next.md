---
title: "Azure Multi-Factor Authentication - 后续步骤"
description: "这个有关 Azure Multi-Factor Authentication 的页面介绍了使用 MFA 可以执行的后续步骤。  其中包括报告、欺诈警报、一次性跳过、自定义语音消息、缓存，受信任的 IP 和应用密码。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d904de6370c1c6bd5116471465ec63fd8999a7ee


---
# <a name="configuring-azure-multi-factor-authentication"></a>配置 Azure Multi-Factor Authentication
在启动并运行 Azure 多重身份验证后，可以参考本文进行管理。  本文涵盖各种主题，有助于充分利用 Azure 多重身份验证。  并非所有版本的 Azure 多重身份验证都提供这些功能。

以下某些功能的配置可在 Azure Multi-Factor Authentication 管理门户中找到。 可以通过两种不同的方式访问 MFA 管理门户，但均通过 Azure 门户访问。 第一种方式是管理 Multi-Factor Auth 提供程序（如果使用基于使用量的 MFA）。 第二种方式是通过 MFA 服务设置。 第二个选项需要 Multi-Factor Auth 提供程序或 Azure MFA、Azure AD Premium 或 Enterprise Mobility Suite 许可证。

若要通过 Azure Multi-Factor Auth 提供程序访问 MFA 管理门户，请以管理员身份登录 Azure 门户，然后选择“Active Directory”选项。 单击“多重身份验证提供程序”选项卡、选择目录，然后单击底部的“管理”按钮。

若要通过“MFA 服务设置”页访问 MFA 管理门户，请以管理员身份登录 Azure 门户，然后选择“Active Directory”选项。 单击目录，然后单击“配置”选项卡。 在“多重身份验证”部分下，选择“管理服务设置”。 在“MFA 服务设置”页底部，单击“转到门户”链接。

| 功能 | 说明 | 涵盖的内容 |
|:--- |:--- |:--- |
| [欺诈警报](#fraud-alert) |管理员可以配置和设置欺诈警报，以便用户针对访问其资源的欺诈性企图进行举报。 |如何设置、配置和举报欺诈行为 |
| [免验证一次](#one-time-bypass) |“一次性跳过”可让用户通过“跳过”Multi-Factor Authentication 来进行身份验证，不过只能跳过一次。 |如何设置和配置“免验证一次” |
| [自定义语音消息](#custom-voice-messages) |自定义语音消息可让你将自己的录音或问候语用于 Multi-Factor Authentication。 |如何设置和配置自定义问候语与消息 |
| [缓存](#caching-in-azure-multi-factor-authentication) |使用缓存可以设置一段特定的时间，使后续的身份验证尝试自动成功。 |如何设置和配置身份验证缓存。 |
| [受信任的 IP](#trusted-ips) |受信任的 IP 是一项 Multi-Factor Authentication 功能，它允许托管或联合租户的管理员跳过对从公司本地 Intranet 登录的用户进行的 Multi-Factor Authentication。 |配置和设置免于进行多重身份验证的 IP 地址 |
| [应用密码](#app-passwords) |应用密码允许非 MFA 感知应用程序免于进行多重身份验证并继续工作。 |有关应用密码的信息。 |
| [为已记住设备和浏览器记住多重身份验证](#remember-multi-factor-authentication-for-devices-users-trust) |允许在用户使用 MFA 成功登录后的指定天数内记住设备。 |有关启用此功能和设置天数的信息。 |
| [可选择验证方法](#selectable-verification-methods) |允许你选择可供用户使用的身份验证方法。 |有关启用或禁用特定身份验证方法（如呼叫或短信）的信息。 |

## <a name="fraud-alert"></a>欺诈警报
管理员可以配置和设置欺诈警报，以便用户针对访问其资源的欺诈性企图进行举报。  用户可以通过移动应用或电话来报告欺诈行为。

### <a name="to-set-up-and-configure-fraud-alert"></a>设置和配置欺诈警报
1. 登录 http://azure.microsoft.com
2. 根据此页顶部的说明导航到 MFA 管理门户。
3. 在 Azure Multi-Factor Authentication 管理门户中，单击“配置”部分下面的“设置”。
4. 在“设置”页的“欺诈警报”部分下，选中“允许用户提交欺诈警报”复选框。
5. 如果你希望在报告欺诈时阻止用户，则勾选“报告欺诈时阻止用户”。
6. 在“用于在初始问候期间报告欺诈的代码”文本框中，输入可以在通话验证期间使用的数字代码。 如果用户输入此代码加上 #（而不是只输入 # 号），则随后会报告欺诈警报。
7. 在底部单击“保存”。

> [!NOTE]
> Microsoft 的默认问候语音将指示用户按 0# 以提交欺诈警报。 如果要使用 0 以外的代码，应根据相应说明记录并上载自己的自定义问候语音。
> 
> 

![云](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>报告欺诈警报
可通过两种途径来报告欺诈警报。  通过移动应用程序或电话。  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>使用移动应用报告欺诈警报
1. 在手机上收到验证信息后，选择它即可启动 Microsoft Authenticator 应用。
2. 若要报告欺诈行为，请单击“取消并报告欺诈行为”。 此时会弹出一个框，显示“组织的 IT 支持人员将收到通知”。
3. 单击“报告欺诈行为”。
4. 在该应用上，单击“关闭”。

![云](./media/multi-factor-authentication-whats-next/report1.png)

![云](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>使用电话报告欺诈行为
1. 当手机收到验证来电时，请接听。  
2. 若要报告欺诈，请输入已配置为与通过电话报告欺诈相对应的代码，然后输入 # 号。 此时将通知你，已提交了欺诈警报。
3. 结束通话。

### <a name="to-view-the-fraud-report"></a>查看欺诈报告
1. 登录 [http://azure.microsoft.com](https://azure.microsoft.com/)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。 此时将显示多重身份验证提供程序列表。
4. 如果你有多个 Multi-Factor Auth 提供程序，请选择要查看“欺诈警报”报告的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。 如果只有一个，则直接单击“管理”。 此时将打开 Azure 多重身份验证管理门户。
5. 在 Azure Multi-Factor Authentication管理门户上，单击左侧“查看报告”下的“欺诈警报”。
6. 指定要在报告中查看的日期范围。 此外，还可以指定任何特定用户名、电话号码和用户状态。
7. 单击“运行”。 此时显示与下方类似的报告。 如果要导出报告，还可以单击“导出到 CSV”。

## <a name="one-time-bypass"></a>一次性跳过
“一次性跳过”可让用户通过“跳过”Multi-Factor Authentication 来进行身份验证，不过只能跳过一次。 跳过是暂时性的，将在指定的秒数后过期。  在移动应用或电话无法接收通知或来电的情况下，你可以启用一次性跳过，使用户能够访问所需的资源。

### <a name="to-create-a-one-time-bypass"></a>创建一次性跳过
1. 登录 http://azure.microsoft.com
2. 根据此页顶部的说明导航到 MFA 管理门户。
3. 在 Azure Multi-Factor Authentication 管理门户中，如果在左侧看到租户或 Azure MFA 提供程序的名称旁边有一个 +，则单击 + 可查看不同的 MFA 服务器复制组以及 Azure 默认组。 单击相应的组。
4. 在“用户管理”下，单击“免验证一次”。
   ![云](./media/multi-factor-authentication-whats-next/create1.png)
5. 在“免验证一次”页上，单击“新建免验证一次”。
6. 输入用户的用户名、“免验证”存在的秒数、免验证的原因，然后单击“免验证”。
   ![云](./media/multi-factor-authentication-whats-next/create2.png)
7. 此时，用户必须在一次性跳过过期之前登录。

### <a name="to-view-the-one-time-bypass-report"></a>查看一次性跳过报告
1. 登录 [http://azure.microsoft.com](https://azure.microsoft.com/)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。 此时将显示多重身份验证提供程序列表。
4. 如果你有多个 Multi-Factor Auth 提供程序，请选择要查看“欺诈警报”报告的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。 如果只有一个，则直接单击“管理”。 此时将打开 Azure 多重身份验证管理门户。
5. 在 Azure Multi-Factor Authentication 管理门户上，单击左侧“查看报告”下的“一次性跳过”。
6. 指定要在报告中查看的日期范围。 此外，还可以指定任何特定用户名、电话号码和用户状态。
7. 单击“运行”。 此时显示与下方类似的报告。 如果要导出报告，还可以单击“导出到 CSV”。

<center>![云](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>自定义语音消息
自定义语音消息可让你将自己的录音或问候语用于 Multi-Factor Authentication。  可以在 Microsoft 录音之外额外使用自定义语音消息，或使用它来取代 Microsoft 录音。

在开始之前，请注意以下事项：

* 当前支持的文件格式为 .wav 和 .mp3。
* 文件大小上限是 5MB。
* 建议对于身份验证消息，长度不要超过 20 秒。 大于该长度的消息可能会导致验证失败，因为用户在消息结束前可能无法响应，验证会超时。

### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>在 Azure 多重身份验证中设置自定义语音消息
1. 使用支持的文件格式之一创建自定义语音消息。
2. 登录 http://azure.microsoft.com
3. 根据此页顶部的说明导航到 MFA 管理门户。
4. 在 Azure Multi-Factor Authentication 管理门户中，单击“配置”部分下面的“语音消息”。
5. 在“语音消息”部分下，单击“新建语音消息”。
   ![云](./media/multi-factor-authentication-whats-next/custom1.png)
6. 在“配置: 新建语音消息”页上，单击“管理声音文件”。
   ![云](./media/multi-factor-authentication-whats-next/custom2.png)
7. 在“配置: 声音文件”页上，单击“上载声音文件”。
   ![云](./media/multi-factor-authentication-whats-next/custom3.png)
8. 在“配置: 上载声音文件”页上，单击“浏览”，然后导航到你的语音消息，单击“打开”。
   ![云](./media/multi-factor-authentication-whats-next/custom4.png)
9. 添加说明，然后单击“上载”。
10. 完成此操作后，系统将显示一条消息，指示文件已成功上载。
11. 在左侧，单击“语音消息”。
12. 在“语音消息”部分下，单击“新建语音消息”。
13. 从“语言”下拉列表中选择语言。
14. 如果此消息用于特定应用程序，请在“应用程序”框中指定该应用程序。
15. 从“消息类型”中，选择将使用新建自定义消息替代的消息类型。
16. 从“声音文件”下拉列表中选择你的声音文件。
17. 单击“创建” 。 系统将显示一条消息，指示语音消息已成功创建。
    ![云](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的缓存
使用缓存可以设置一段特定的时间，使后续的身份验证尝试自动成功。 缓存主要应用场合是当第一个请求仍在进行时，本地系统（例如 VPN）发送多个验证请求。 这样，后续请求将在用户成功验证后自动成功完成。 请注意，Azure AD 的登录并未打算使用缓存。

### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>在 Azure 多重身份验证中设置缓存
1. 登录 http://azure.microsoft.com
2. 根据此页顶部的说明导航到 MFA 管理门户。
3. 在 Azure Multi-Factor Authentication 管理门户中，单击“配置”部分下面的“缓存”。
4. 在“配置缓存”页上，单击“新建缓存”。
5. 选择缓存类型和缓存秒数。 单击“创建”。

<center>![云](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>受信任的 IP
受信任的 IP 是一项 Multi-Factor Authentication 功能，它允许托管或联合租户的管理员跳过对从公司本地 Intranet 登录的用户进行的 Multi-Factor Authentication。 这些功能适用于拥有 Azure AD Premium、Enterprise Mobility Suite 或 Azure Multi-Factor Authentication 许可证的 Azure AD 租户。

| AZURE AD 租户类型	 | 可用的受信任 IP 选项 |
|:--- |:--- |
| 托管 |特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可免于进行多重身份验证的 IP 地址范围。 |
| 联合 |<li>所有联合用户 - 所有从组织内部登录的联合用户都将使用 AD FS 发布的声明免于进行多重身份验证。</li><li>特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可免于进行多重身份验证的 IP 地址范围。 |

这种跳过只能从公司 Intranet 进行。 例如，如果只选择所有联合用户，当用户从公司 Intranet 外部登录时，即使该用户出示 AD FS 声明，也必须使用多重身份验证进行身份验证。 下表描述在启用受信任 IP 的情况下，公司网络内部和外部何时需要 Multi-Factor Authentication 与应用密码。

| 受信任的 IP 已启用 | 受信任的 IP 已禁用 |
|:--- |:--- |:--- |
| 在公司网络内部 |浏览器流量不需要多重身份验证。 |
| 对于富客户端应用，如果用户尚未创建任何应用密码，使用一般密码即可。 创建应用密码之后，用户必须使用应用密码。 |富客户端应用需要应用密码 |
| 在公司网络外部 |浏览器流量需要 Multi-Factor Authentication。 |
| 富客户端应用需要应用密码。 |富客户端应用需要应用密码。 |

### <a name="to-enable-trusted-ips"></a>启用受信任的 IP
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击要设置受信任 IP 的目录。
4. 在选择的目录上，单击“配置”。
5. 在“Multi-Factor Authentication”部分中，单击“管理服务设置”。
6. 在“服务设置”页的“受信任的 IP”下，选择以下任一项：
   
   * 对于来自 Intranet 的联合用户发出的请求 – 所有从公司网络登录的联合用户都能使用 AD FS 发出的声明跳过 Multi-Factor Authentication。
   * 对于来自特定公共 IP 范围的请求 – 使用 CIDR 表示法在提供的框中输入 IP 地址。 例如：xxx.xxx.xxx.0/24 表示 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 范围内的 IP 地址；xxx.xxx.xxx.xxx/32 表示单个 IP 地址。 你最多可以输入 50 个 IP 地址范围。
7. 单击“保存”。
8. 应用更新后，单击“关闭”。

![受信任的 IP](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>应用密码
在 Office 2010 或更低版本和 Apple Mail 等某些应用中，无法使用多重身份验证。  若要使用这些应用，你需要使用“应用密码”来替换传统密码。  应用密码可让应用程序跳过 Multi-Factor Authentication 并继续工作。

> [!NOTE]
> 适用于 Office 2013 客户端的现代身份验证
> 
> Office 2013 客户端（包括 Outlook）现在支持新的身份验证协议，并且可以启用对 Multi-Factor Authentication 的支持。  这意味着一旦启用后，就不需要对 Office 2013 客户端使用应用密码。  有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。
> 
> 

### <a name="important-things-to-know-about-app-passwords"></a>有关应用密码的重要事项
下面是你应该知道的有关应用密码的重要事项列表。

* 用户可能有多个应用密码，从而增加了密码被盗的可能性。 由于应用密码很难记住，因此用户可能会将密码写下来。 不建议这么做并应阻止这么做，因为使用应用密码登录只需进行单重身份验证。
* 缓存密码并在本地方案中使用该密码的应用可能启动失败，因为超出组织 ID 范畴的应用密码不可识别。 例如 Exchange 电子邮件，这些电子邮件位于本地但存档邮件位于云中。 无法使用同一密码。
* 实际的密码将自动生成，并非由用户提供。 这是因为自动生成的密码会使攻击者更难进行猜测，因而更安全。
* 目前，每个用户有 40 个密码的限制。 系统将提示你删除现有的应用密码之一，以便创建新的应用密码。
* 对用户的帐户启用多重身份验证后，可以在大多数非浏览器客户端（例如 Outlook 和 Lync）上使用应用密码，但无法通过 Windows PowerShell 等非浏览器应用程序使用应用密码执行管理操作，即使该用户具有管理帐户。  请确保使用强密码创建服务帐户以运行 PowerShell 脚本，而不要为该帐户启用多重身份验证。

> [!WARNING]
> 在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 这是因为本地身份验证需要域密码，而云身份验证需要应用密码。
> 
> 

### <a name="naming-guidance-for-app-passwords"></a>应用密码命名指南
建议应用密码名称应能反映使用应用密码的设备。 例如，如果你有一台安装了非浏览器应用（如 Outlook、Word 和 Excel）的便携式计算机，则只需创建一个名为 Laptop 的应用密码，即可在所有这些应用程序中使用该应用密码。 虽然你可以为所有这些应用程序创建单独的密码，但不建议这样做。 建议的方式是每个设备使用一个应用密码。

<center>![云](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>联合 (SSO) 应用密码
Azure AD 支持与本地 Windows Server Active Directory 域服务 (AD DS) 联合。 如果你的组织与 Azure AD 联合 (SSO) 并且你要使用 Azure Multi-Factor Authentication，则以下是你在使用应用密码时应了解的重要信息。 这仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，我们从不转到标识提供者 (IdP)。 密码将存储在组织 ID 中。 如果用户离开公司，则该信息必须通过 DirSync 实时流到组织 ID 中。 帐户禁用/删除可能需要长达三个小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵循“本地客户端访问控制”设置
* 本地身份验证日志记录/审核功能不可用于应用密码
* 对于 Microsoft Lync 2013 客户端，更多最终用户培训是必需的。 有关必需的步骤，请参阅如何将电子邮件中的密码更改为应用密码。
* 在对客户端使用 Multi-Factor Authentication 时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。 对于针对本地基础结构进行身份验证的客户端，你会使用组织用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，你会使用应用密码。

例如，假设你有一个由下列内容组成的体系结构：

* 你要将 Active Directory 的本地实例与 Azure AD 联合
* 你要使用 Exchange Online
* 你要使用本地专用的 Lync
* 你要使用 Azure Multi-Factor Authentication

![验证](./media/multi-factor-authentication-whats-next/federated.png)

 在这些情况下，你必须执行以下操作：

* 登录到 Lync 时，使用你组织的用户名和密码。
* 当尝试通过连接到 Exchange Online 的 Outlook 客户端访问通讯簿时，请使用应用密码。

### <a name="allowing-app-password-creation"></a>允许用户创建应用密码
默认情况下，用户无法创建应用密码。  为此，必须启用此功能。  若要允许用户创建应用密码，请使用以下过程。

#### <a name="to-enable-users-to-create-app-passwords"></a>允许用户创建应用密码
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下单击要为其启用此功能的用户的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。  
6. 在“Multi-Factor Authentication”页的顶部，单击“服务设置”。
7. 确保选中了“允许用户创建用于登录到非浏览器应用程序的应用密码”旁边的单选按钮。

![创建应用密码](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>创建应用密码
用户可以在最初注册时创建应用密码。  注册过程结束时，系统将提供一个选项用于创建应用密码。

此外，用户日后还可以通过在 Azure 门户和 Office 365 门户中更改设置来创建应用密码。

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>在 Office 365 门户中创建应用密码
- - -
1. 登录 Office 365 门户
2. 在右上角选择设置小组件
3. 在左侧选择“其他安全性验证”
4. 在右侧，选择“更新用于帐户安全性的电话号码”
5. 在验证页的顶部选择应用密码
6. 单击“创建” 
7. 输入应用密码的名称，然后单击“下一步”
8. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。

<center>![云](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>在 Azure 门户中创建应用密码
- - -
1. 登录到 Azure 经典门户。
2. 在顶部，右键单击你的用户名并选择“其他安全性验证”。
3. 在验证页的顶部选择应用密码
4. 单击“创建” 
5. 输入应用密码的名称，然后单击“下一步”
6. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。

![应用密码](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>在没有 Office 365 或 Azure 订阅的情况下创建应用密码
- - -
1. 登录 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在顶部选择配置文件。
3. 单击你的用户名并选择“其他安全性验证”。
4. 在验证页的顶部选择应用密码
5. 单击“创建” 
6. 输入应用密码的名称，然后单击“下一步”
7. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。

![应用密码](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>记住用户信任的设备的 Multi-Factor Authentication
记住用户信任的设备和浏览器的 Multi-Factor Authentication 是针对所有 MFA 用户的一项免费功能。  此功能允许你在用户使用 MFA 执行成功登录后的指定天数内为用户提供绕过 MFA 的选项。 这可以提高你的用户的可用性。

但是，由于允许用户记住受信任设备的 MFA，此功能可能会降低帐户安全性。 要确保帐户的安全性，请在出现以下任一情况时还原用户设备的 Multi-Factor Authentication：

* 如果你的公司帐户受到安全威胁
* 如果记住的设备丢失或被盗

> [!NOTE]
> 此功能通过浏览器 Cookie 缓存方式实现。 如果未启用浏览器 Cookie，此功能则无法实现。
> 
> 

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>如何启用/禁用“记住 Multi-Factor Authentication”
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在 Active Directory 下，单击要为设备设置“记住多重身份验证”的目录。
4. 在选择的目录上，单击“配置”。
5. 在“Multi-Factor Authentication”部分中，单击“管理服务设置”。
6. 在“服务设置”页面上的“管理用户设备设置”下，选择/取消选择“允许用户记住受信任设备的多重身份验证”选项。
   ![记住设备](./media/multi-factor-authentication-whats-next/remember.png)
7. 设置允许暂停的天数。 默认值为 14 天。
8. 单击“保存”。
9. 单击“关闭”。

## <a name="selectable-verification-methods"></a>可选择验证方法
在云和本地版本中，可以选择可供用户使用的验证方法。 下表提供每个方法的简要概述。

当用户注册 MFA 帐户时，可以选择已启用选项之外的首选验证方法。 [设置我的双重验证帐户](multi-factor-authentication-end-user-first-time.md)中介绍了注册过程指导

| 方法 | 说明 |
|:--- |:--- |
| 拨打电话 |向身份验证电话拨打自动语音呼叫。 用户接听电话，并按电话键盘上的 # 进行身份验证。 此电话号码不会同步到本地 Active Directory。 |
| 向手机发送短信 |向用户发送包含验证码的短信。 系统会提示用户使用验证码回复短信或在登录界面中输入验证码。 |
| 通过移动应用发送通知 |在此模式下，Microsoft Authenticator 应用可防止对帐户进行未经授权的访问并停止欺诈性事务。 此功能是使用推送到你的手机或已注册设备上的推送通知来完成的。 你可以直接查看通知，如果该通知是合法的，请点击“验证”。 否则，你可以选择“拒绝”或选择拒绝并报告欺诈性通知。 有关举报欺诈性通知的信息，请参阅“如何针对多重身份验证使用‘拒绝并举报欺诈行为’功能”。</br></br>Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 通过移动应用发送验证码 |在此模式下，Microsoft Authenticator 应用可用作生成 OATH 验证码所需的软件令牌。 然后可以将此验证码与用户名和密码一起输入，进行第二种形式的身份验证。</li><br><p> Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="how-to-enabledisable-authentication-methods"></a>如何启用/禁用身份验证方法
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在 Active Directory 中，单击要对其启用或禁用身份验证方法的目录。
4. 在选择的目录上，单击“配置”。
5. 在“Multi-Factor Authentication”部分中，单击“管理服务设置”。
6. 在“服务设置”页上的验证选项下，选择/取消选择要使用的选项。</br></br>
   ![验证选项](./media/multi-factor-authentication-whats-next/authmethods.png)
7. 单击“保存”。
8. 单击“关闭”。




<!--HONumber=Nov16_HO3-->


