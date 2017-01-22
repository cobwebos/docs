---
title: "Azure MFA 服务器的用户门户 | Microsoft 文档"
description: "这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何开始使用 Azure MFA 和用户门户。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>为 Azure 多重身份验证服务器部署用户门户
在用户门户中，管理员可以安装和配置 Azure 多重身份验证用户门户。 用户门户是一个 IIS 网站，可让用户注册 Azure 多重身份验证以及维护其帐户。 用户可以在下次登录期间更改其电话号码、更改其 PIN 码或选择绕过双重验证。

用户可使用其普通用户名和密码登录到用户门户，然后根据电话呼叫完成双重验证或回答安全问题，完成其身份验证。 如果允许用户注册，用户可在首次登录到用户门户时配置其电话号码和 PIN 码。

可以设置用户门户管理员并向其授予添加新用户和更新现有用户的权限。

<center>![设置](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>在与 Azure 多重身份验证服务器所在的同一台服务器上部署用户门户
若要在与 Azure 多重身份验证服务器所在的同一台服务器上安装用户门户，必须满足以下先决条件：

* 安装 IIS，包括 asp.net 和 IIS 6 元数据库兼容性（适用于 IIS 7 或更高版本）
* 登录用户必须具有计算机和域的管理员权限（如果适用）。  这是因为帐户需有创建 Active Directory 安全组的权限。

### <a name="to-deploy-the-user-portal"></a>部署用户门户
1. 在 Azure 多重身份验证服务器中，单击左侧菜单中的“用户门户”图标，然后单击“安装用户门户”。
2. 单击“资源组名称” 的 Azure 数据工厂。
3. 单击“下一步”。
4. 如果计算机已加入域，但未将 Active Directory 配置为保护用户门户与 Azure 多重身份验证服务之间的通信，则会显示 Active Directory 步骤。 单击“下一步”按钮可自动完成此配置。
5. 单击“下一步”。
6. 单击“资源组名称” 的 Azure 数据工厂。
7. 单击“**关闭**”。
8. 从任何计算机打开 Web 浏览器，然后导航到安装用户门户的 URL（例如 https://www.publicwebsite.com/MultiFactorAuth）。 确保未显示证书警告或错误。

<center>![设置](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>在不同的服务器上部署 Azure 多重身份验证服务器用户门户
若要允许 Microsoft 验证器应用与用户门户通信，需满足以下要求： 

* 必须使用 v6.0 或更高版本的 Azure Multi-Factor Authentication 服务器。
* 用户门户必须安装在运行 Microsoft® Internet Information Services (IIS) 6.x、IIS 7.x 或更高版本的面向 Internet 的 Web 服务器上。
* 使用 IIS 6.x 时，请确保 ASP.NET v2.0.50727 已安装、注册并设置为“允许”。
* 使用 IIS 7.x 或更高版本时必需的角色服务包括 ASP.NET 和 IIS 6 元数据库兼容性。
* 应使用 SSL 证书保护用户门户。
* Azure 多重身份验证 Web 服务 SDK 必须安装在 Azure 多重身份验证服务器所安装的服务器上的 IIS 6.x、IIS 7.x 或更高版本中。
* 必须使用 SSL 证书保护 Azure Multi-Factor Authentication Web 服务 SDK。
* 用户门户必须能够通过 SSL 连接到 Azure 多重身份验证 Web 服务 SDK。
* 用户门户必须能够使用“PhoneFactor 管理员”安全组中某个服务帐户的凭据，在 Azure 多重身份验证 Web 服务 SDK 中进行身份验证。 如果 Azure Multi-Factor Authentication 服务器运行在已加入域的服务器上，则此服务帐户和组存在于 Active Directory 中。 如果 Azure Multi-Factor Authentication 服务器未加入域，则此服务帐户和组存在于本地的该服务器上。

在除 Azure Multi-Factor Authentication 服务器以外的其他服务器上安装用户门户需要完成以下三个步骤：

1. 安装 Web 服务 SDK
2. 安装用户门户
3. 在 Azure Multi-Factor Authentication 服务器中配置用户门户设置

### <a name="step-1-install-the-web-service-sdk"></a>步骤 1：安装 Web 服务 SDK
如果 Azure 多重身份验证 Web 服务 SDK 未安装在运行 Azure 多重身份验证服务器的服务器上，请转到该服务器并打开 Azure 多重身份验证服务器。 单击“Web 服务 SDK”图标，然后单击“安装 Web 服务 SDK”。 遵照显示的说明操作。 

必须使用 SSL 证书保护 Web 服务 SDK。 为此可以使用自签名证书，但必须将该证书导入服务器上的本地计算机帐户的“受信任的根证书颁发机构”存储中。 现在，Web 服务 SDK 可以在发起 SSL 连接时信任该证书。

<center>![设置](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>步骤 2：安装用户门户
在不同的服务器上安装用户门户之前，请注意以下最佳实践：

* 在面向 Internet 的 Web 服务器上打开 Ｗeb 浏览器，并导航到已输入到 web.config 文件中的 Web 服务 SDK 的 URL，这会很有用。 如果浏览器可以成功访问 Web 服务，它应提示你输入凭据。 输入已输入到 web.config 文件中的用户名和密码（与文件中显示的完全相同）。 确保未显示证书警告或错误。
* 如果反向代理或防火墙目前位于用户门户 Web 服务器之前并执行 SSL 卸载，你可以编辑用户门户 web.config 文件，并向 `<appSettings>` 节中添加以下键，使用户门户使用 http 而不是 https。

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>安装用户门户
1. 在运行 Azure 多重身份验证服务器的服务器上，打开 Windows 资源管理器，并导航到 Azure 多重身份验证服务器所安装到的文件夹（例如 C:\Program Files\Multi-Factor Authentication Server）。 为要安装用户门户的服务器相应地选择 32 位或 64 位版本的 MultiFactorAuthenticationUserPortalSetup 安装文件。 将安装文件复制到面向 Internet 的服务器。
2. 在面向 Internet 的 Web 服务器上，必须使用管理员权限运行安装文件。 执行此操作的最简单方法是，以管理员身份打开命令提示符，并导航到安装文件所复制到的位置。
3. 运行 MultiFactorAuthenticationUserPortalSetup64 安装文件，并根据需要更改站点和虚拟目录的名称。
4. 完成用户门户的安装后，请浏览到 C:\inetpub\wwwroot\MultiFactorAuth（或基于虚拟目录名称的相应目录）并编辑 web.config 文件。
5. 找到 USE_WEB_SERVICE_SDK 键，并将值从 false 更改为 true。 找到 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 和 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 键，并将其值设为“PhoneFactor 管理员”安全组中服务帐户的用户名和密码（请参阅“要求”部分）。 请务必在行末引号之间输入用户名和密码 (value=""/>)。 应使用限定的用户名（例如“域\用户名”或“计算机\用户名”）。
6. 找到“pfup_pfwssdk_PfWsSdk”设置，并将值从“http://localhost:4898/PfWsSdk.asmx”更改为在 Azure 多重身份验证服务器上运行的 Web 服务 SDK 的 URL（例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx）。 由于此连接使用 SSL，而 SSL 证书是根据服务器名称颁发的，因此请按照服务器名称而不是 IP 地址引用 Web 服务 SDK。 如果服务器名称无法解析为面向 Internet 的服务器的 IP 地址，请在该服务器上的 hosts 文件中添加一个条目，以将 Azure Multi-Factor Authentication 服务器的名称映射到其 IP 地址。 进行更改之后，保存 web.config 文件。

    有关编辑配置文件的详细信息，请参阅 [Secure your resources using Azure Multi-Factor Authentication Server with AD FS](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file)（使用 Azure 多重身份验证服务器与 AD FS 保护资源）。

7. 如果已安装用户门户的网站（例如默认网站）尚未与公开签名的证书绑定，请在服务器上安装证书，打开 IIS 管理器并将证书绑定到该网站。
8. 从任何计算机打开 Web 浏览器，然后导航到已安装用户门户的 URL（例如 https://www.publicwebsite.com/MultiFactorAuth）。 确保未显示证书警告或错误。

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>步骤 3：在 Azure 多重身份验证服务器中配置用户门户设置
安装用户门户后，需要配置 Azure 多重身份验证服务器，使其能够与 Azure 门户配合工作。

Azure Multi-Factor Authentication 服务器为用户门户提供了多个选项。  下表列出了这些选项及其用途的说明。

| 用户门户设置 | 说明 |
|:--- |:--- |
| 用户门户 URL | 输入托管门户的 URL。 |
| 主要身份验证 | 指定在登录门户时要使用的身份验证类型。  Windows、Radius 或 LDAP 身份验证。 |
| 允许用户登录 | 允许用户在用户门户的登录页中输入用户名和密码。  如果未选择此项，框将会灰显。 |
| 允许用户注册 | 将用户定向到设置屏幕并提示输入其他信息（例如电话号码），使用户能够注册多重身份验证。 备用电话的提示可让用户指定备用电话号码。 第三方 OATH 令牌的提示可让用户指定第三方 OATH 令牌。 |
| 允许用户启动一次性跳过 | 允许用户启动一次性跳过。  如果用户设置此选项，该设置将在下次登录时生效。 出现跳过秒数的提示时，将显示一个框让用户更改默认的 300 秒。 否则，一次性跳过只生效 300 秒。 |
| 允许用户选择方法 | 允许用户指定其主要联系方法。  这可以是电话呼叫、短信、移动应用或 OATH 令牌。 |
| 允许用户选择语言 | 允许用户更改通话、短信、移动应用或 OATH 令牌使用的语言。 |
| 允许用户激活移动应用 | 允许用户生成激活代码，以完成服务器使用的移动应用激活过程。  还可以设置允许用户在其上激活该应用的设备数（1 到 10 个）。 |
| 使用安全提问进行回退 | 在双重验证失败的情况下允许回答安全提问。  可以指定必须正确回答的安全提问数。 |
| 允许用户关联第三方 OATH 令牌 | 允许用户指定第三方 OATH 令牌。 |
| 使用 OATH 令牌进行回退 | 在双重验证失败的情况下允许使用 OATH 令牌。 你也可以指定会话超时（以分钟为单位）。 |
| 启用日志记录 | 在用户门户上启用日志记录。 日志文件位于：C:\Program Files\Multi-Factor Authentication Server\Logs。 |

启用这些设置后，当用户登录用户门户时，就会看到其中的大多数设置。

![用户门户设置](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication 服务器中配置用户门户设置
1. 在 Azure 多重身份验证服务器中，单击“用户门户”图标。 在“设置”选项卡上的“用户门户 URL”文本框中，输入用户门户的 URL。 此 URL 将插入发送给导入到 Azure 多重身份验证服务器的用户的电子邮件（如果已启用电子邮件功能）。
2. 选择要在用户门户中使用的设置。 例如，如果允许用户控制其身份验证方法，请确保选中“允许用户选择方法”以及这些用户可以从中选择的方法。
3. 单击右上角的“帮助”链接，帮助了解显示的任何设置。

<center>![设置](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>“管理员”选项卡
此选项只是让你添加具有管理权限的用户。  添加管理员时，可以精细调整他们可以获得的权限。 单击“添加”按钮，选择用户及其权限，然后单击“添加”即可。

![用户门户管理员](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>安全提问
如果选择了“使用安全提问进行回退”选项，可在此选项卡中指定用户必须回答的安全提问。  Azure 多重身份验证提供了可以使用的默认问题。 可以更改问题的顺序，或添加自己的问题。  添加自己的问题时，还可以指定用于显示这些问题的语言。

![用户门户安全提问](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
在此选项卡中可以配置用户门户，使用 SAML 接受来自标识提供者的声明。  可以指定超时会话、验证证书和注销重定向 URL。

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>受信任的 IP
使用此选项卡可以指定单个 IP 地址或指定 IP 地址范围，当用户从其中一个地址登录时，就不需要完成双重验证。

![用户门户中受信任的 IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>自助用户注册
如果要让用户登录并注册，必须在“设置”选项卡下面选择“允许用户登录”和“允许用户注册”选项。 请记住，选择的设置会影响用户的登录体验。

例如，当用户首次登录到用户门户时，会转到“Azure 多重身份验证用户设置”页。  根据 Azure Multi-Factor Authentication 的配置方式，用户也许能够选择身份验证方法。  

如果他们选择“语音呼叫”验证方法，或已预先配置为使用该方法，此页将提示用户输入主要电话号码和分机（如果有）。  他们还可以输入备用电话号码。  

![用户门户中受信任的 IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

如果要求用户在身份验证时使用 PIN 码，此页还会提示他们创建 PIN 码。  在输入其电话号码和 PIN 码（如果适用）之后，用户可单击“立即打电话给我进行身份验证”按钮。  Azure 多重身份验证将通过用户的主电话号码执行电话呼叫验证。  用户必须接听电话并输入其 PIN 码（如果适用），然后按 # 转到自我注册过程的下一步。   

如果用户选择“短信”验证方法或已预先配置为使用该方法，则该页将提示用户输入其手机号码。  如果要求用户在身份验证时使用 PIN 码，此页还会提示他们输入 PIN 码。  在输入其电话号码和 PIN 码（如果适用）之后，用户可单击“立即发短信给我进行身份验证”按钮。  Azure 多重身份验证将通过用户的手机执行短信验证。  用户将收到包含一次性密码 (OTP) 的短信，然后应使用该 OTP 和 PIN 码（如果适用）回复该短信。

![用户门户 SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

如果用户选择“移动应用”验证方法，该页将提示用户在其设备上安装 Microsoft 验证器应用并生成激活代码。  安装该应用后，用户可单击“生成激活代码”按钮。    

> [!NOTE]
> 若要使用 Microsoft 验证器应用，用户必须为其设备启用推送通知。

然后，该页将显示激活代码和 URL 以及条形码图片。  如果要求用户在身份验证时使用 PIN 码，此页还会提示他们输入 PIN 码。  用户将激活代码和 URL 输入到 Microsoft 验证器应用中，或使用条形码扫描仪扫描条形码图片，然后单击“激活”按钮。    

完成激活后，用户可单击“立即对我进行身份验证”按钮。  Azure 多重身份验证将通过用户的移动应用执行验证。  用户必须输入其 PIN 码（如果适用），并按其移动应用中的“身份验证”按钮，以转到自我注册过程的下一步。  

如果管理员已将 Azure Multi-Factor Authentication 服务器配置为收集安全提问和答案，则用户将转到“安全提问”页。  用户必须选择四个安全问题，并提供其所选问题的答案。    

![用户门户安全提问](./media/multi-factor-authentication-get-started-portal/secq.png)  

现在，用户自我注册已完成，用户可登录到用户门户。  用户在以后可随时重新登录到用户门户，以更改其电话号码、PIN 码、身份验证方法和安全问题（如果其管理员允许）。




<!--HONumber=Jan17_HO1-->


