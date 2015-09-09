<properties 
	pageTitle="Azure Multi-Factor Authentication - 后续步骤" 
	description="这个有关 Azure Multi-Factor Authentication 的页面介绍了使用 MFA 可以执行的后续步骤。其中包括报告、欺诈警报、一次性跳过、自定义语音消息、缓存，受信任的 IP 和应用密码。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="07/02/2015" 
	wacn.date=""/>

# 配置 Azure Multi-Factor Authentication

在启动并运行 Azure Multi-Factor Authentication 后，可以参考以下文章来对其进行管理。该文章涵盖各种主题，可让你充分利用 Azure Multi-Factor Authentication。请注意，并非所有版本的 Azure Multi-Factor Authentication 都提供这些功能。

功能| 说明| 涵盖的内容
:------------- | :------------- | :------------- | 
[欺诈警报](#fraud-alert)|你可以配置和设置欺诈警报，使你的用户可以针对访问其资源的欺诈性企图进行举报。|如何设置、配置和报告欺诈行为
[一次性跳过](#one-time-bypass) |“一次性跳过”可让用户通过“跳过”Multi-Factor Authentication 来进行身份验证，不过只能跳过一次。|如何设置和配置一次性跳过
[自定义语音消息](#custom-voice-messages) |自定义语音消息可让你将自己的录音或问候语用于 Multi-Factor Authentication。 |如何设置和配置自定义问候语与消息
[缓存](#caching)|使用缓存可以设置一段特定的时间，使后续的身份验证尝试自动成功。 |如何设置和配置身份验证缓存。
[受信任的 IP](#trusted-ips)|受信任的 IP 是一项 Multi-Factor Authentication 功能，它允许托管或联合租户的管理员跳过对从公司本地 Intranet 登录的用户进行的 Multi-Factor Authentication。|配置和设置免于进行 Multi-Factor Authentication 的 IP 地址	
[应用密码](#app-passwords)|应用密码允许非 MFA 感知应用程序跳过 Multi-Factor Authentication 并继续工作。|有关应用密码的信息。
[暂停记住的设备和浏览器的 Multi-Factor Authentication（公共预览版）](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|可让你在用户使用 MFA 成功登录后的设置天数内暂停 MFA。|有关启用此功能和设置天数的信息。




## 欺诈警报
你可以配置和设置欺诈警报，使你的用户可以针对访问其资源的欺诈性企图进行举报。用户可以通过移动应用或电话来报告欺诈行为。

### 设置和配置欺诈警报


1. 登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。
4. 如果你有多个Multi-Factor Auth 提供程序，请选择你要在其上启用欺诈警报的提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。
5. 在 Azure Multi-Factor Authentication 管理门户上，单击左侧的“设置”。
6. 在“欺诈警报”部分下，勾选“允许用户提交欺诈警报”。
7. 如果你希望在报告欺诈时阻止用户，则勾选“报告欺诈时阻止用户”。
8. 在“用于在初始问候期间报告欺诈的代码”下的框中输入可以在呼叫验证期间使用的数字代码。如果用户输入此代码（而非 # 号），则随后会报告欺诈警报。
9. 在底部单击“保存”。

<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### 报告欺诈警报
可通过两种途径来报告欺诈警报。通过移动应用程序或电话。

### 使用移动应用报告欺诈警报
<ol>
<li>当验证发送到你的手机时，单击该验证即会启动 Multi-Factor Authentication 应用。</li>
<li>若要报告欺诈行为，请单击“取消并报告欺诈行为”。此时会弹出一个框，其中显示“你组织的 IT</li> 支持人员将会收到通知”。单击“报告欺诈行为”。
<li>在该应用上，单击“关闭”。</li></ol>

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### 使用电话报告欺诈行为
<ol>
<li>当验证电话打进你的电话时，请接听。</li>
<li>若要报告欺诈，请输入已配置为与通过电话报告欺诈相对应的代码，然后输入 # 号。此时将通知你，已提交了欺诈警报。</li>
<li>结束通话。</li></ol>

### 查看欺诈报告

1. 登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。
4. 如果你有多个 Multi-Factor Auth 提供程序，请选择要查看“欺诈警报”报告的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。
5. 在 Azure Multi-Factor Authentication管理门户上，单击左侧“查看报告”下的“欺诈警报”。
6. 指定要在报告中查看的日期范围。此外，还可以指定任何特定用户名、电话号码和用户状态。
7. 单击“运行”。这将显示与下面的报告类似的报告。如果要导出报告，还可以单击“导出到 CSV”。

## 一次性跳过

“一次性跳过”可让用户通过“跳过”Multi-Factor Authentication 来进行身份验证，不过只能跳过一次。跳过是暂时性的，将在指定的秒数后过期。在移动应用或电话无法接收通知或来电的情况下，你可以启用一次性跳过，使用户能够访问所需的资源。

### 创建一次性跳过

<ol>
<li>登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)</li>
<li>在左侧选择“Active Directory”。</li>
<li>在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。</li>
<li>如果你有多个 Multi-Factor Auth 提供程序，请选择与需要为其创建一次性跳过的用户目录关联的提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。</li>
<li>在 Azure Multi-Factor Authentication 管理门户上，单击左侧“用户管理”下的“设置”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create1.png)</center>

<li>在“一次性跳过”页上，单击“新建一次性跳过”。</li>
<li>输入用户的用户名、“跳过”将存在的秒数、跳过的原因，然后单击“跳过”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create2.png)</center>

<li>此时，用户必须在一次性跳过过期之前登录。</li>

### 查看一次性跳过报告

1. 登录到 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。
4. 如果你有多个 Multi-Factor Auth 提供程序，请选择要查看“欺诈警报”报告的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。
5. 在 Azure Multi-Factor Authentication 管理门户上，单击左侧“查看报告”下的“一次性跳过”。
6. 指定要在报告中查看的日期范围。此外，还可以指定任何特定用户名、电话号码和用户状态。
7. 单击“运行”。这将显示与下面的报告类似的报告。如果要导出报告，还可以单击“导出到 CSV”。

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## 自定义语音消息

自定义语音消息可让你将自己的录音或问候语用于 Multi-Factor Authentication。可以在 Microsoft 录音之外额外使用自定义语音消息，或使用它来取代 Microsoft 录音。

在开始之前，请注意以下事项：

- 当前支持的文件格式为 .wav 和 .mp3。
- 文件大小上限是 5MB。
- 建议对于身份验证消息，长度不要超过 20 秒。大于该长度的消息可能会导致验证失败，因为用户在消息结束前可能无法响应，验证会超时。



### 在 Azure Multi-Factor Authentication 中设置自定义语音消息
<ol>
<li>使用支持的文件格式之一创建自定义语音消息。参阅下面的“自定义语音消息建议”。</li>
<li>登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)</li>
<li>在左侧选择“Active Directory”。</li>
<li>在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。</li>
<li>如果你有多个 Multi-Factor Auth 提供程序，请选择要在其上配置自定义语音消息的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。</li>
<li>在 Azure Multi-Factor Authentication 管理门户上，单击左侧的“语音消息”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)</center>

<li>在“语音消息”部分下，单击“新建语音消息”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)</center>

<li>在“配置: 新建语音消息”页上，单击“管理声音文件”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)</center>

<li>在“配置: 声音文件”页上，单击“上载声音文件”。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)</center>

<li>在“配置: 上载声音文件”页上，单击“浏览”，然后导航到你的语音消息，单击“打开”。</li>
<li>添加说明，然后单击“上载”。</li>
<li>完成此操作后，你将看到一条消息，指示已成功上载文件。</li>
<li>在左侧，单击“语音消息”。</li>
<li>在“语音消息”部分下，单击“新建语音消息”。</li>
<li>从“语言”下拉列表中选择语言。</li>
<li>如果此消息用于特定应用程序，请在“应用程序”框中指定该应用程序。</li>
<li>从“消息类型”中，选择将使用我们的新建自定义消息重写的消息类型。</li>
<li>从“声音文件”下拉列表中选择你的声音文件。</li>
<li>单击“创建”。你将看到一条消息，指示已成功创建语音消息。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>


## Azure Multi-Factor Authentication 中的缓存

使用缓存可以设置一段特定的时间，使后续的身份验证尝试自动成功。如果用户在这段时间进行身份验证，便不需要等待电话或短信。



### 在 Azure Multi-Factor Authentication 中设置缓存
<ol>

1. 登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。
4. 如果你有多个Multi-Factor Auth 提供程序，请选择你要在其上启用欺诈警报的提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。
5. 在 Azure Multi-Factor Authentication 管理门户上，单击左侧的“缓存”。
6. 在“配置缓存”页上，单击“新建缓存”。
7. 选择缓存类型和缓存秒数。单击“创建”。


<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## 受信任的 IP

受信任的 IP 是一项 Multi-Factor Authentication 功能，它允许托管或联合租户的管理员跳过对从公司本地 Intranet 登录的用户进行的 Multi-Factor Authentication。这些功能适用于拥有 Azure AD Premium、Enterprise Mobility Suite 或 Azure Multi-Factor Authentication 许可证的 Azure AD 租户。

 
AZURE AD 租户类型| 可用的受信任 IP 选项
:------------- | :------------- | 
托管|特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可跳过 Multi-Factor Authentication 的 IP 地址范围。
联合|<li>所有联合用户 - 所有从组织内部登录的联合用户，都能使用 AD FS 发出的声明跳过 Multi-Factor Authentication。</li><li>特定 IP 地址范围 – 对于从公司 Intranet 登录的用户，管理员可以指定可跳过 Multi-Factor Authentication 的 IP 地址范围。

这种跳过只能从公司 Intranet 进行。例如，如果你只选择所有联合用户，当用户从公司 Intranet 外部登录时，即使该用户出示 AD FS 声明，也仍必须使用 Multi-Factor Authentication 进行身份验证。下表描述在启用受信任 IP 的情况下，公司网络内部和外部何时需要 Multi-Factor Authentication 与应用密码。


|受信任的 IP 已启用| 受信任的 IP 已禁用
:------------- | :------------- | :------------- | 
在公司网络内部|浏览器流量不需要 Multi-Factor Authentication。|浏览器流量需要 Multi-Factor Authentication。
|对于富客户端应用，如果用户尚未创建任何应用密码，使用一般密码即可。创建应用密码之后，用户必须使用应用密码。|富客户端应用需要应用密码
在公司网络外部|浏览器流量需要 Multi-Factor Authentication。|浏览器流量需要 Multi-Factor Authentication。
|富客户端应用需要应用密码。|富客户端应用需要应用密码。

### 启用受信任的 IP

 
<ol>
<li>登录到 Azure 管理门户。</li>
<li>在左侧单击“Active Directory”。</li>
<li>在“目录”下，单击要设置受信任 IP 的目录。</li>
<li>在选择的目录上，单击“配置”。</li>
<li>在“Multi-Factor Authentication”部分中，单击“管理服务设置”。</li>
<li>在“服务设置”页的“受信任的 IP”下，选择以下任一项：
<ul>
<li>对于来自 Intranet 的联合用户发出的请求 – 所有从公司网络登录的联合用户都能使用 AD FS 发出的声明跳过 Multi-Factor Authentication。

<li>对于来自特定公共 IP 范围的请求 – 使用 CIDR 表示法在提供的框中输入 IP 地址。例如：xxx.xxx.xxx.0/24 表示 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 范围内的 IP 地址；xxx.xxx.xxx.xxx/32 表示单个 IP 地址。最多可以输入 12 个 IP 地址范围。</li></ul>


<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>


<li>单击“保存”。</li>
<li>应用更新后，单击“关闭”。</li>
## 应用密码

在 Office 2010 或更低版本和 Apple Mail 等某些应用中，无法使用 Multi-Factor Authentication。若要使用这些应用，你需要使用“应用密码”来替换传统密码。应用密码可让应用程序跳过 Multi-Factor Authentication 并继续工作。

>[AZURE.NOTE]适用于 Office 2013 客户端的现代身份验证
>
> Office 2013 客户端（包括 Outlook）现在支持新的身份验证协议，并且可以启用对 Multi-Factor Authentication 的支持。这意味着一旦启用后，就不需要对 Office 2013 客户端使用应用密码。有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

### 有关应用密码的重要事项

下面是你应该知道的有关应用密码的重要事项列表。

身份验证体验|对于基于浏览器的应用|对于非基于浏览器的应用
:------------- | :------------- | :------------- 
|<ul><li>身份验证的第一个因素在本地执行</li><li>第二个因素是由云标识执行的基于电话的方法。</li>|<ul><li>管理员和用户可以使用应用密码来登录。

- 用户可能有多个应用密码，从而增加了密码被盗的可能性。由于应用密码很难记住，因此用户可能会将密码写下来。不建议这么做并应阻止这么做，因为使用应用密码登录只需进行单重身份验证。
- 缓存密码并在本地方案中使用该密码的应用可能会启动失败，因为超出组织 ID 范畴的应用密码是不可识别的。一个示例是 Exchange 电子邮件，这些电子邮件位于本地但存档邮件位于云中。同一密码将无法正常工作。
- 实际的密码将自动生成，并非由用户提供。这是因为自动生成的密码会使攻击者更难进行猜测，因而更安全。
- 目前，每个用户有 40 个密码的限制。系统将提示你删除旧应用密码（现有的应用密码之一），以便创建新的应用密码。


### 应用密码命名指南
建议应用密码名称应能反映使用应用密码的设备。例如，如果你有一台安装了非浏览器应用（如 Outlook、Word 和 Excel）的便携式计算机，则只需创建一个名为 Laptop 的应用密码，即可在所有这些应用程序中使用该应用密码。虽然你可以为所有这些应用程序创建单独的密码，但不建议这样做。建议的方式是每个设备使用一个应用密码。


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### 联合 (SSO) 应用密码
Azure AD 支持与本地 Windows Server Active Directory 域服务 (AD DS) 联合。如果你的组织与 Azure AD 联合 (SSO) 并且你要使用 Azure Multi-Factor Authentication，则以下是你在使用应用密码时应了解的重要信息。这仅适用于联合 (SSO) 客户。

- 应用密码由 Azure AD 进行验证，从而绕过了联合。仅在设置应用密码时，才会主动使用联合。
- 与被动流程不同，对于联合 (SSO) 用户，我们从不转到标识提供者 (IdP)。密码将存储在组织 ID 中。如果用户离开公司，则该信息必须通过 DirSync 实时流到组织 ID 中。帐户禁用/删除可能需要长达 3 小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
- 应用密码不遵循“本地客户端访问控制”设置
- 本地身份验证日志记录/审核功能不可用于应用密码
- 对于 Microsoft Lync 2013 客户端，更多最终用户培训是必需的。有关必需的步骤，请参阅如何将电子邮件中的密码更改为应用密码。
- 在对客户端使用 Multi-Factor Authentication 时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。对于针对本地基础结构进行身份验证的客户端，你会使用组织用户名和密码。对于针对 Azure AD 进行身份验证的客户端，你会使用应用密码。

例如，假设你有一个由下列内容组成的体系结构：

- 你要将 Active Directory 的本地实例与 Azure AD 联合
- 你要使用 Exchange Online
- 你要使用本地专用的 Lync
- 你要使用 Azure Multi-Factor Authentication


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 在这些情况下，你必须执行以下操作：

- 登录到 Lync 时，使用你组织的用户名和密码。
- 当尝试通过连接到 Exchange Online 的 Outlook 客户端访问通讯簿时，请使用应用密码。

### 允许用户创建应用密码
默认情况下，用户无法创建应用密码。为此，必须启用此功能。若要允许用户创建应用密码，请使用以下过程。

#### 允许用户创建应用密码



1. 登录到 Azure 管理门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下单击要为其启用此功能的用户的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。此时将打开“Multi-Factor Authentication”页。
6. 在“Multi-Factor Authentication”页的顶部，单击“服务设置”。
7. 确保选中了“允许用户创建用于登录到非浏览器应用程序的应用密码”旁边的单选按钮。

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

### 创建应用密码
用户可以在最初注册时创建应用密码。注册过程结束时，系统将提供一个选项用于创建应用密码。

此外，用户日后还可以通过在 Azure 门户和 Office 365 门户中更改设置来创建应用密码。

### 在 Office 365 门户中创建应用密码
--------------------------------------------------------------------------------


1. 登录到 Office 365 门户
2. 在右上角选择设置小组件
3. 在左侧选择“其他安全性验证”
4. 在右侧，选择“更新用于帐户安全性的电话号码”
5. 在验证页的顶部选择应用密码
6. 单击“创建”
7. 输入应用密码的名称，然后单击“下一步”
8. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### 在 Azure 门户中创建应用密码
--------------------------------------------------------------------------------
1. 登录到 Azure 管理门户
3. 在顶部，右键单击你的用户名并选择“其他安全性验证”。
5. 在验证页的顶部选择应用密码
6. 单击“创建”
7. 输入应用密码的名称，然后单击“下一步”
8. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### 在没有 Office 365 或 Azure 订阅的情况下创建应用密码
--------------------------------------------------------------------------------
1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. 在顶部选择配置文件。
3. 单击你的用户名并选择“其他安全性验证”。
5. 在验证页的顶部选择应用密码
6. 单击“创建”
7. 输入应用密码的名称，然后单击“下一步”
8. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## 暂停记住的设备和浏览器的 Multi-Factor Authentication（公共预览版）

使用暂停记住的设备和浏览器的 Multi-Factor Authentication 功能，管理员能够为用户提供相关选项，使其在成功执行 MFA 后，将 MFA 暂停一定的天数。这是面向所有 MFA 用户的免费功能，可以帮助用户提高可用性。但是，由于允许用户暂停 MFA，此功能可能会降低帐户安全性。

若要确保用户帐户受到保护，用户应还原你的设备的 Multi-Factor Authentication，以防发生以下两种情况之一：

- 如果你的公司帐户受到安全威胁
- 如果记住的设备丢失或被盗

> [AZURE.NOTE]此功能通过浏览器 Cookie 缓存方式实现。如果你没有启用浏览器 Cookie，则此功能无法实现。

### 如何启用/禁用和设置暂停对已记住设备的 MFA

<ol>
<li>登录到 Azure 管理门户。</li>
<li>在左侧单击“Active Directory”。</li>
<li>在 Active Directory 下，单击你要设置记住的设备的 Multi-Factor Authentication 的目录。</li>
<li>在选择的目录上，单击“配置”。</li>
<li>在“Multi-Factor Authentication”部分中，单击“管理服务设置”。</li>
<li>在“服务设置”页上的“管理用户设备设置”下，选择/取消选择“允许用户通过记住其设备暂停 Multi-Factor Authentication”选项。</li>
![暂停设备](./media/multi-factor-authentication-manage-users-and-devices/suspend.png)
<li>设置你希望允许暂停的天数。默认值为 14 天。</li>
<li>单击“保存”。</li>
<li>单击“关闭”。</li>

<!---HONumber=69-->