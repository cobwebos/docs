<properties 
	pageTitle="MFA 服务器移动应用 Web 服务入门" 
	description="Azure Multi-Factor Authentication 应用提供一个附加的带外身份验证选项。它可以让 MFA 服务器对用户使用推送通知。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="08/04/2016" 
	wacn.date=""/>

# MFA 服务器移动应用 Web 服务入门

Azure Multi-Factor Authentication 应用提供一个附加的带外身份验证选项。Azure Multi-Factor Authentication 会将通知推送到用户智能手机或平板电脑上的 Azure Multi-Factor Authentication 应用中，而不会在用户登录期间对用户进行自动电话呼叫或向其发送短信。用户只需在该应用中点击“身份验证”（或输入 PIN 码并点击“身份验证”）即可登录。

若要使用 Azure Multi-Factor Authentication 应用，需要满足以下先决条件，才能使该应用成功地与移动应用 Web 服务进行通信：

- 有关硬件和软件要求，请参阅“硬件和软件要求”
- 必须使用 v6.0 或更高版本的 Azure Multi-Factor Authentication 服务器
- 移动应用 Web 服务必须安装在运行 Microsoft® Internet Information Services (IIS) IIS 7.x 或更高版本、面向 Internet 的 Web 服务器上。有关 IIS 的详细信息，请参阅 [IIS.NET](http://www.iis.net/)。
- 确保 ASP.NET v4.0.30319 已安装、注册并设置为“允许”
- 必需的角色服务包括 ASP.NET 和 IIS 6 元数据库兼容性
- 移动应用 Web 服务必须可通过公共 URL 访问
- 必须使用 SSL 证书保护移动应用 Web 服务。
- Azure 多重身份验证 Web 服务 SDK 必须安装在 Azure 多重身份验证服务器信任的服务器上的 IIS 7.x 或更高版本中
- 必须使用 SSL 证书保护 Azure Multi-Factor Authentication Web 服务 SDK。
- 移动应用 Web 服务必须能够通过 SSL 连接到 Azure Multi-Factor Authentication Web 服务 SDK
- 移动应用 Web 服务必须能够使用属于名为“PhoneFactor 管理员”的安全组的服务帐户凭据，通过 Azure Multi-Factor Authentication Web 服务 SDK 进行身份验证。如果 Azure Multi-Factor Authentication 服务器运行在已加入域的服务器上，则此服务帐户和组存在于 Active Directory 中。如果 Azure Multi-Factor Authentication 服务器未加入域，则此服务帐户和组存在于本地的该服务器上。


在除 Azure Multi-Factor Authentication 服务器以外的其他服务器上安装用户门户需要完成以下三个步骤：

1. 安装 Web 服务 SDK
2. 安装移动应用 Web 服务
3. 在 Azure Multi-Factor Authentication 服务器中配置移动应用设置
4. 为最终用户激活 Azure Multi-Factor Authentication 应用

## 安装 Web 服务 SDK

如果 Azure Multi-Factor Authentication Web 服务 SDK 未安装在运行 Azure Multi-Factor Authentication 服务器的服务器上，请转到该服务器并打开 Azure Multi-Factor Authentication 服务器。单击“Web 服务 SDK”图标和“安装 Web 服务 SDK...”按钮，然后按照显示的说明进行操作。必须使用 SSL 证书保护 Web 服务 SDK。自签名证书可用于此目的，但必须将该证书导入到用户门户 Web 服务器上的本地计算机帐户的“受信任的根证书颁发机构”存储中，以便该服务器在发起 SSL 连接时信任该证书。

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## 安装移动应用 Web 服务
在安装移动应用 Web 服务之前，请注意以下事项：

- 如果 Azure Multi-Factor Authentication 用户门户已安装在面向 Internet 的服务器上，则可以从用户门户的 web.config 文件中复制用户名、密码和 Web 服务 SDK 的 URL。
- 在面向 Internet 的 Web 服务器上打开 Ｗeb 浏览器，并导航到已输入到 web.config 文件中的 Web 服务 SDK 的 URL，这会很有用。如果浏览器可以成功访问 Web 服务，它应提示你输入凭据。输入已输入到 web.config 文件中的用户名和密码（与文件中显示的完全相同）。确保未显示证书警告或错误。
- 如果反向代理或防火墙位于移动应用 Web 服务 Web 服务器的前面，并在执行 SSL 卸载，则可以编辑移动应用 Web 服务 web.config 文件并将以下项添加到 <appSettings> 节，使移动应用 Web 服务可以使用 http 而非 https。但是，从移动应用到防火墙/反向代理仍然需要 SSL。<add key="SSL\_REQUIRED" value="false"/>

### 安装移动应用 Web 服务

<ol>
<li>在 Azure Multi-Factor Authentication 服务器上打开 Windows 资源管理器并导航到安装 Azure Multi-Factor Authentication 服务器的文件夹（例如，C:\Program Files\Azure Multi-Factor Authentication）。根据移动应用 Web 服务将安装到的服务器的需要，选择 32 位或 64 位版本的 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup 安装文件。将安装文件复制到面向 Internet 的服务器。</li> 

<li>在面向 Internet 的 Web 服务器上，必须使用管理员权限运行安装文件。执行此操作的最简单方法是，以管理员身份打开命令提示符，并导航到安装文件所复制到的位置。</li>  

<li>运行 Multi-Factor AuthenticationMobileAppWebServiceSetup 安装文件，根据需要更改站点，并将虚拟目录更改为短名称，如“PA”。建议使用较短的虚拟目录名称，因为在激活期间用户必须在移动设备中输入移动应用 Web 服务 URL。</li> 

<li>完成 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 的安装后，请浏览到 C:\inetpub\wwwroot\PA（或基于虚拟目录名称的相应目录）并编辑 web.config 文件。</li>  

<li>找到 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 和 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 键，并将其值设为属于“PhoneFactor 管理员”安全组的服务帐户的用户名和密码（请参见上面的“要求”部分）。这可能是用作 Azure Multi-Factor Authentication 用户门户标识的帐户（如果之前已安装）。请务必在行末引号之间输入用户名和密码 (value=""/>)。建议使用限定的用户名（例如“域\用户名”或“计算机\用户名”）。</li>  

<li>找到“pfMobile 应用 Web Service_pfwssdk_PfWsSdk”设置，并将值从“http://localhost:4898/PfWsSdk.asmx”更改为在 Azure Multi-Factor Authentication 服务器上运行的 Web 服务 SDK 的 URL（例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx）。由于对此连接使用了 SSL，你必须按服务器名称（而非 IP 地址）引用 Web 服务 SDK，因为已针对服务器名称颁发 SSL 证书，而所用的 URL 必须与证书上的名称相匹配。如果服务器名称无法解析为面向 Internet 的服务器的 IP 地址，请在该服务器上的 hosts 文件中添加一个条目，以将 Azure Multi-Factor Authentication 服务器的名称映射到其 IP 地址。进行更改之后，保存 web.config 文件。</li>  

<li>如果已安装移动应用 Web 服务的网站（例如默认网站）尚未与公开签名的证书绑定，请在服务器上安装证书（如果尚未安装），打开 IIS 管理器并将证书绑定到该网站。</li>  

<li>从任何计算机打开 Web 浏览器，然后导航到已安装移动应用 Web 服务的 URL（例如 https://www.publicwebsite.com/PA）。确保未显示证书警告或错误。</li> 

### 在 Azure Multi-Factor Authentication 服务器中配置移动应用设置
安装移动应用 Web 服务后，你需要配置 Azure Multi-Factor Authentication 服务器，使其能够与门户配合工作。

#### 在 Azure Multi-Factor Authentication 服务器中配置移动应用设置

1. 在 Azure Multi-Factor Authentication 服务器中，单击“用户门户”图标。如果允许用户控制其身份验证方法，请在“设置”选项卡上的“允许用户选择方法”下，选中“移动应用”。如果未启用此功能，则最终用户将需要联系技术支持才能完成移动应用的激活。
2. 选中“允许用户激活移动应用”框。
3. 选中“允许用户注册”框。
4. 单击“移动应用”图标。
5. 输入用于在安装 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 时创建的虚拟目录的 URL。可以在提供的空白处输入帐户名称。此公司名称将显示在移动应用程序中。如果留空，则将显示在 Azure 管理门户中创建的 Multi-Factor Auth 提供程序的名称。



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
 

<!---HONumber=Mooncake_0919_2016-->