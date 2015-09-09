<properties 
	pageTitle="将 Azure MFA 服务器与 Windows Server 2012 R2 AD FS 配合使用来保护云和本地资源" 
	description="这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与 Windows Server 2012 R2 上的 AD FS 配合使用。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="06/02/2015" 
	wacn.date=""/>


# 将 Azure Multi-Factor Authentication 服务器与 Windows Server 2012 R2 AD FS 配合使用来保护云和本地资源

如果你的组织已与 Azure AD 联合，并且你在本地或云中拥有要保护的资源，则可以通过使用 Azure Multi-Factor Authentication 服务器并将其配置为使用 AD FS（以便为高价值终结点触发 Multi-Factor Authentication）来保护这些资源。

本文介绍如何将 Azure Multi-Factor Authentication 服务器与 Windows Server 2012 R2 中的 AD FS 配合使用。有关有关配合 AD FS 2.0 使用 Azure Multi-Factor Authentication 的信息，请参阅[将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用来保护云和本地资源](/documentation/articles/multi-factor-authentication-get-started-adfs-adfs2)。

## 使用 Azure Multi-Factor Authentication 服务器保护 Windows Server 2012 R2 AD FS

在安装 Azure Multi-Factor Authentication 服务器时，可以使用以下两个选项：

- 在与 AD FS 所在的同一台服务器本地安装 Azure Multi-Factor Authentication 服务器 
- 在 AD FS 服务器本地上安装 Azure Multi-Factor Authentication 适配器，并在另一台计算机上安装 MFA 服务器

在开始之前，请注意以下信息：

- Azure Multi-Factor Authentication 服务器不一定非要安装在 AD FS 联合服务器上，但必须在运行 AD FS 的 Windows Server 2012 R2 上安装适用于 AD FS 的 Multi-Factor Authentication 适配器。你可以将服务器安装在其他计算机上（只要它是受支持的版本），并将 AD FS 适配器单独安装在 AD FS 联合服务器上。有关如何单独安装适配器的说明，请参阅以下过程。

- 登录帐户必须具有在 Active Directory 中创建安全组的权限。

- Multi-Factor Authentication AD FS 适配器安装向导在 Active Directory 中创建名为 PhoneFactor 管理员的安全组，并将你的联合服务的 AD FS 服务帐户添加到该组中。建议你在域控制器上确认 PhoneFactor 管理员组已真正创建，而且 AD FS 服务帐户是该组的成员。需要时，在域控制器上手动将 AD FS 服务帐户添加到 PhoneFactor 管理员组。
  

### 在 AD FS 所在的同一台服务器本地安装 Azure Multi-Factor Authentication 服务器

1. 在 AD FS 联合服务器上下载并安装 Azure Multi-Factor Authentication 服务器。有关安装 Azure Multi-Factor Authentication 服务器的信息，请参阅 [Azure Multi-Factor Authentication 服务器入门](/documentation/articles/multi-factor-authentication-get-started-server)
2. 在 Azure Multi-Factor Authentication 服务器用户界面中，选择“AD FS”图标并选择“允许用户注册”和“允许用户选择方法”的选项。
3. 选择任何附加选项。
4. 单击“安装 AD FS 配接器”。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. 如果计算机已加入域，但保护 AD FS 适配器与 Multi-Factor Authentication 服务之间通信的 Active Directory 配置未完成，则将显示 Active Directory 步骤。单击“下一步”按钮以自动完成此配置，或者选中“跳过自动 Active Directory 配置并手动配置设置”复选框并单击“下一步”。
6. 如果计算机尚未加入域，且保护 AD FS 适配器与 Multi-Factor Authentication 服务之间通信的本地组配置未完成，则将显示本地组步骤。单击“下一步”按钮以自动完成此配置，或者选中“跳过自动本地组配置并手动配置设置”复选框并单击“下一步”。
7. 这样将显示安装向导，单击“下一步”以允许 Azure Multi-Factor Authentication 服务器创建 PhoneFactor 管理员组，并将 AD FS 服务帐户添加到 PhoneFactor 管理员组。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. 在“启动安装程序”步骤中，单击“下一步”按钮。
9. 在Multi-Factor Authentication AD FS 适配器安装程序中，单击“下一步”。
10. 安装完成后，单击“关闭”。
11. 安装适配器后，必须将它注册到 AD FS。打开 Windows PowerShell 并运行以下脚本：C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![云](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. 现在，我们需要在 AD FS 中编辑全局身份验证策略，以便使用我们新注册的适配器。在 AD FS 管理控制台中，导航到“身份验证策略”节点，并在“Multi-Factor Authentication”部分下，单击“全局设置”子部分旁边的“编辑”链接。在“编辑全局身份验证策略”窗口中，选择“Multi-Factor Authentication”作为其他身份验证方法，然后单击“确定”。适配器已注册为 WindowsAzureMultiFactorAuthentication。必须重新启动 AD FS 服务才能使注册生效。

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

现在，Multi-Factor Authentication 服务器已设置要与 AD FS 配合使用的附加身份验证提供程序。

## 使用 Web 服务 SDK 单独安装 AD FS 适配器
1. 在运行 Multi-Factor Authentication 服务器的服务器上安装 Web 服务 SDK。
2. 将 MultiFactorAuthenticationAdfsAdapterSetup64.msi、Register-MultiFactorAuthenticationAdfsAdapter.ps1、Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 和 MultiFactorAuthenticationAdfsAdapter.config 文件从 \Program Files\Multi-Factor Authentication Server 目录复制到你计划安装 AD FS 适配器的服务器上。
3. 运行 MultiFactorAuthenticationAdfsAdapterSetup64.msi。
4. 在 Multi-Factor Authentication AD FS 适配器安装程序中，单击“下一步”执行安装。
5. 安装完成后，单击“关闭”按钮。
6. 通过执行以下操作，编辑 MultiFactorAuthenticationAdfsAdapter.config 文件：

MultiFactorAuthenticationAdfsAdapter.config 步骤| 子步骤
:------------- | :------------- |
将 UseWebServiceSdk 节点设置为 true。||
将 WebServiceSdkUrl 设置为Multi-Factor Authentication Web 服务 SDK 的 URL。||
选项 1 - 使用用户名和密码来配置 Web 服务 SDK。|<ol><li>将 WebServiceSdkUsername 设置为 PhoneFactor 管理员安全组成员帐户。使用 <domain><username> 格式。<li>将 WebServiceSdkPassword 设置为相应的帐户密码。</li></ol>
选项 2 - 使用客户端证书来配置 Web 服务 SDK。|<ol><li>从运行 Web 服务 SDK 的服务器的证书颁发机构获取客户端证书。</li><li>将客户端证书导入运行 Web 服务 SDK 的服务器上的本地计算机“个人”证书存储区。注意：请确保证书颁发机构的公共证书位于“受信任的根证书”下。</li><li>将客户端证书的公钥和私钥导出为 .pfx 文件。</li><li>将 Base-64 格式的公钥导出为 .cer 文件。</li><li>在服务器管理器中，确认“Web 服务器(IIS)\Web 服务器\安全\客户端证书映射身份验证”功能已安装。</li><li>如果没有安装，请选择“添加角色和功能”以添加此功能。</li><li>在 IIS 管理器中，双击包含 Web 服务 SDK 虚拟目录的网站中的“配置编辑器”。注意：必须在网站级别上执行此操作，而不是在虚拟目录级别上执行，这一点非常重要。</li><li>导航到 system.webServer/security/authentication/iisClientCertificateMappingAuthentication 部分。</li><li>将 enabled 设置为 true。</li><li>将 oneToOneCertificateMappingsEnabled 设置为 true。</li><li>单击 oneToOneMappings 旁边的“...”按钮。</li><li>单击“添加”链接。</li><li>打开先前导出的 Base-64 .cer 文件。删除 -----BEGIN CERTIFICATE-----、-----END CERTIFICATE----- 和所有换行符。复制生成的字符串。</li><li>将证书设置为在上一步中复制的字符串。</li><li>将 enabled 设置为 true。</li><li>将 userName 设置为 PhoneFactor 管理员安全组成员帐户。请使用 <domain><username> 格式。</li><li>将 password 设置为相应的帐户密码。</li><li>关闭“集合编辑器”。</li><li>单击“应用”链接。</li><li>导航到 Web 服务 SDK 虚拟目录。</li><li>双击“身份验证”。</li><li>确认 ASP.NET Impersonation 和 Basic Authentication 为“启用”，其他所有项目为“禁用”。</li><li>再次导航到 Web 服务 SDK 虚拟目录。</li><li>双击“SSL 设置”。</li><li>将“客户端证书”设置为“接受”并单击“应用”。</li><li>将先前导出的 .pfx 文件复制到运行 AD FS 适配器的服务器。</li><li>将 .pfx 文件导入本地计算机“个人”证书存储区。</li><li>从右键单击菜单中选择“管理私钥”，并授予对 Active Directory 联合身份验证服务登录所用帐户的读取权限。</li><li>打开客户端证书，并从“详细信息”选项卡复制指纹。</li><li>在 MultiFactorAuthenticationAdfsAdapter.config 文件中，将 WebServiceSdkCertificateThumbprint 设置为在上一步复制的字符串。</li></ol>
编辑 Register-MultiFactorAuthenticationAdfsAdapter.ps1 脚本，将 -ConfigurationFilePath <path> 添加到 Register-AdfsAuthenticationProvider 命令末尾，其中 <path> 是 MultiFactorAuthenticationAdfsAdapter.config 文件的完整路径。|


在 PowerShell 中运行 \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 脚本以注册适配器。适配器已注册为 WindowsAzureMultiFactorAuthentication。必须重新启动 AD FS 服务才能使注册生效。




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=69-->