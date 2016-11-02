<properties
	pageTitle="将 Azure 多重身份验证服务器与 Windows Server 2012 R2 AD FS 配合使用来保护云资源和本地资源 | Microsoft Azure"
	description="本文介绍如何开始将 Azure 多重身份验证与 Windows Server 2012 R2 中的 AD FS 配合使用。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>


# 将 Azure 多重身份验证服务器与 Windows Server 2012 R2 中的 AD FS 配合使用来保护云资源和本地资源

如果组织使用 Active Directory 联合身份验证服务 (AD FS)，同时想要保护云资源或本地资源，可以部署 Azure 多重身份验证服务器并将其配置为与 AD FS 配合工作。这种配置将为重要的终结点触发多重身份验证。

本文介绍如何将 Azure 多重身份验证服务器与 Windows Server 2012 R2 中的 AD FS 配合使用。请阅读如何[将 Azure 多重身份验证服务器与 AD FS 2.0 配合使用来保护云资源和本地资源](multi-factor-authentication-get-started-adfs-adfs2.md)，了解详细信息。

## 使用 Azure 多重身份验证服务器保护 Windows Server 2012 R2 AD FS

安装 Azure 多重身份验证服务器时，可以使用以下选项：

- 在与 AD FS 所在的同一台服务器上本地安装 Azure 多重身份验证服务器
- 在 AD FS 服务器上本地安装 Azure 多重身份验证适配器，在另一台计算机上安装多重身份验证服务器

在开始之前，请注意以下信息：

- 不需要在 AD FS 服务器上安装 Azure 多重身份验证服务器。但是，必须在运行 AD FS 的 Windows Server 2012 R2 上安装用于 AD FS 的多重身份验证适配器。可以将服务器安装在其他计算机上（如果支持该服务器版本），将 AD FS 适配器单独安装在 AD FS 联合服务器上。请参阅以下过程，了解如何单独安装适配器。
- 设计多重身份验证服务器的 AD FS 适配器时，预期 AD FS 可以将信赖方的名称传递给此适配器，并且可以将此名称用作应用程序名称。但是，结果并非如此。如果组织使用短信或移动应用身份验证方法，“公司设置”中定义的字符串将包含占位符 <$*application\_name*$>。使用 AD FS 适配器时不会自动替换此占位符。因此，建议在保护 AD FS 时，从相应的字符串中删除此占位符。

- 用于登录的帐户必须拥有在 Active Directory 服务中创建安全组的用户权限。

- 多重身份验证 AD FS 适配器安装向导将在 Active Directory 实例中创建名为 PhoneFactor Admins 的安全组，然后将联合身份验证服务的 AD FS 服务帐户添加到此组。建议在域控制器上检查是否确实创建了 PhoneFactor Admins 组，以及 AD FS 服务帐户是否是此组的成员。如果需要，请在域控制器上手动将 AD FS 服务帐户添加到 PhoneFactor Admins 组。
- 请阅读如何[为 Azure 多重身份验证服务器部署用户门户](multi-factor-authentication-get-started-portal.md)，了解有关通过用户门户安装 Web 服务 SDK 的信息。


### 在 AD FS 服务器上本地安装 Azure 多重身份验证服务器

1. 在 AD FS 联合服务器上下载并安装 Azure 多重身份验证服务器。请阅读 [Azure 多重身份验证服务器入门](multi-factor-authentication-get-started-server.md)，了解详细信息。
2. 在 Azure 多重身份验证服务器管理控制台中单击“AD FS”图标，然后选择“允许用户注册”和“允许用户选择方法”选项。
3. 选择要为组织指定的其他任何选项。
4. 单击“安装 AD FS 适配器”。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. 如果计算机已加入域，但保护 AD FS 适配器与多重身份验证服务之间通信的 Active Directory 配置未完成，将显示“Active Directory”窗口。单击“下一步”自动完成此配置，或者选中“跳过自动 Active Directory 配置并手动配置设置”复选框并单击“下一步”。
6. 如果计算机尚未加入域，且保护 AD FS 适配器与多重身份验证服务之间通信的本地组配置未完成，将显示“本地组”窗口。单击“下一步”自动完成此配置，或者选中“跳过自动本地组配置并手动配置设置”复选框并单击“下一步”。
7. 在安装向导中单击“下一步”。Azure 多重身份验证服务器将创建 PhoneFactor Admins 组，并将 AD FS 服务帐户添加到 PhoneFactor Admins 组。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. 在“启动安装程序”页中单击“下一步”。
9. 在多重身份验证 AD FS 适配器安装程序中单击“下一步”。
10. 安装完成后，单击“关闭”。
11. 安装适配器后，必须将它注册到 AD FS。打开 Windows PowerShell 并运行以下命令：<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. 在 AD FS 中编辑全局身份验证策略，以便使用新注册的适配器。在 AD FS 管理控制台中，转到“身份验证策略”节点。在“多重身份验证”部分中，单击“全局设置”部分旁边的“编辑”链接。在“编辑全局身份验证策略”窗口中，选择“多重身份验证”作为附加身份验证方法，然后单击“确定”。适配器已注册为 WindowsAzureMultiFactorAuthentication。必须重新启动 AD FS 服务才能使注册生效。

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

现在，多重身份验证服务器已设置为与 AD FS 配合使用的附加身份验证提供程序。

## 使用 Web 服务 SDK 安装 AD FS 适配器的独立实例
1. 在运行多重身份验证服务器的服务器上安装 Web 服务 SDK。
2. 将以下文件从 \\Program Files\\Multi-Authentication Server 目录复制到要在其上安装 AD FS 适配器的服务器：
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. 运行 MultiFactorAuthenticationAdfsAdapterSetup64.msi 安装文件。
4. 在多重身份验证 AD FS 适配器安装程序中，单击“下一步”执行安装。
5. 安装完成后，单击“关闭”。
6. 通过执行以下操作，编辑 MultiFactorAuthenticationAdfsAdapter.config 文件：

|MultiFactorAuthenticationAdfsAdapter.config 步骤| 子步骤|
|:------------- | :------------- |
|将 **UseWebServiceSdk** 节点设置为 **true**。||
|将 **WebServiceSdkUrl** 的值设置为多重身份验证 Web 服务 SDK 的 URL。</br></br>示例：**https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**</br></br>其中 certificatename 为证书名称。 ||
|配置 Web 服务 SDK。<br><br>*选项 1*：使用用户名和密码|<ol type="a"><li>将 **WebServiceSdkUsername** 的值设置为 PhoneFactor Admins 安全组的成员帐户。使用 &lt;域&gt;&#92;&lt;用户名&gt; 格式。<li>将 **WebServiceSdkPassword** 的值设置为相应的帐户密码。</li></ol>
|配置 Web 服务 SDK（*续*）<br><br>*选项 2*：使用客户端证书|<ol type="a"><li>从证书颁发机构为运行 Web 服务 SDK 的服务器获取客户端证书。了解如何[获取客户端证书](https://technet.microsoft.com/library/cc770328.aspx)。</li><li>将客户端证书导入运行 Web 服务 SDK 的服务器上的本地计算机个人证书存储中。注意：请确保证书颁发机构的公共证书位于“受信任的根证书”存储中。</li><li>将客户端证书的公钥和私钥导出为 .pfx 文件。</li><li>将 Base-64 格式的公钥导出为 .cer 文件。</li><li>在服务器管理器中，确认“Web 服务器 (IIS)”\\“Web 服务器”\\“安全性”\\“IIS 客户端证书映射身份验证”功能已安装。如果没有安装，请选择“添加角色和功能”添加此功能。</li><li>在 IIS 管理器中，双击包含 Web 服务 SDK 虚拟目录的网站中的“配置编辑器”。注意：必须在网站级别而不是虚拟目录级别执行此操作。</li><li>转到 **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** 节。</li><li>将 **enabled** 设置为 **true**。</li><li>将 **oneToOneCertificateMappingsEnabled** 设置为 **true**。</li><li>单击 **oneToOneMappings** 旁边的“...”按钮，然后单击“添加”链接。</li><li>打开前面导出的 Base-64 .cer 文件。删除 *-----BEGIN CERTIFICATE-----*、*-----END CERTIFICATE-----* 和所有换行符。复制生成的字符串。</li><li>将 **certificate** 设置为在上一步骤中复制的字符串。</li><li>将 **enabled** 设置为 **true**。</li><li>将 **userName** 设置为 PhoneFactor Admins 安全组的成员帐户。使用 &lt;域&gt;&#92;&lt;用户名&gt; 格式。</li><li>将 password 设置为相应的帐户密码，然后关闭配置编辑器。</li><li>单击“应用”链接。</li><li>在 Web 服务 SDK 虚拟目录中，双击“身份验证”。</li><li>确认“ASP.NET 模拟”和“基本身份验证”设置为“已启用”，其他所有项设置为“已禁用”。</li><li>在 Web 服务 SDK 虚拟目录中，双击“SSL 设置”。</li><li>将“客户端证书”设置为“接受”，然后单击“应用”。</li><li>将前面导出的 .pfx 文件复制到运行 AD FS 适配器的服务器。</li><li>将 .pfx 文件导入本地计算机个人证书存储中。</li><li>单击右键并选择“管理私钥”，然后授予对 Active Directory 联合身份验证服务登录所用帐户的读取访问权限。</li><li>打开客户端证书，复制“详细信息”选项卡中的指纹。</li><li>在 MultiFactorAuthenticationAdfsAdapter.config 文件中，将 **WebServiceSdkCertificateThumbprint** 设置为在上一步骤中复制的字符串。</li></ol>
| 编辑 Register-MultiFactorAuthenticationAdfsAdapter.ps1 脚本，将 *-ConfigurationFilePath &lt;path&gt;* 添加到 `Register-AdfsAuthenticationProvider` 命令的末尾，其中，*&lt;path&gt;* 是 MultiFactorAuthenticationAdfsAdapter.config 文件的完整路径。||

若要注册适配器，请在 PowerShell 中运行 \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 脚本。适配器已注册为 WindowsAzureMultiFactorAuthentication。必须重新启动 AD FS 服务才能使注册生效。

<!---HONumber=AcomDC_0921_2016-->