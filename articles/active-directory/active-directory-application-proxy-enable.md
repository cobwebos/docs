<properties
	pageTitle="启用 Azure AD 应用程序代理 | Azure"
	description="在 Azure 经典门户中，打开应用程序代理并为反向代理安装连接器。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="06/17/2016"
	wacn.date=""/>

# 在 Azure 门户中启用应用程序代理

本文将指导你完成在 Azure AD 中为云目录启用 Microsoft Azure AD 应用程序代理的步骤。此过程包括

- 在专用网络中安装应用程序代理连接器，它会使你的网络保持连接到代理服务。
- 将连接器注册到 Microsoft Azure AD 租户订阅。

如果你不了解应用程序代理可以做些什么，可了解有关[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)的详细信息。

## 应用程序代理先决条件
在启用和使用应用程序代理服务之前，你需要：

- Microsoft Azure AD [基本或高级版订阅](/documentation/articles/active-directory-editions)以及你本人为全局管理员的 Azure AD 目录。
- 可以安装应用程序代理连接器，运行 Windows Server 2012 R2 或 Windows 8.1 或更高版本的服务器。该服务器将 HTTPS 请求发送到云中的应用程序代理服务，并且需要与你要发布的应用程序建立 HTTPS 连接。
- 如果路径中有防火墙，请确保防火墙已打开，以便连接器向应用程序代理发出 HTTPS (TCP) 请求。连接器将连同属于高级域 msappproxy.net 和 servicebus.windows.net 的子域一起使用这些端口。确保打开以下用于**出站**流量的**所有**端口：

	| 端口号 | 说明 |
	| --- | --- |
	| 80 | 启用出站 HTTP 流量以进行安全验证。 |
	| 443 | 针对 Azure AD 启用用户身份验证（只有连接器注册过程才需要） |
	| 10100¨C10120 | 启用发送回代理的 LOB HTTP 响应 |
	| 9352、5671 | 为传入请求启用连接器到 Azure 服务之间的通信。 |
	| 9350 | 可选，提高传入请求的性能 |
	| 8080 | 启用连接器启动序列和连接器自动更新 |
	| 9090 | 启用连接器注册（只有连接器注册过程才需要） |
	| 9091 | 启用连接器信任证书自动续订 |

如果你的防火墙根据发起用户强制实施流量，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。此外，请确保为 NT Authority\\System 启用端口 8080。


## 步骤 1：在 Azure AD 中启用应用程序代理
1. 在 [Azure 经典门户](https://manage.windowsazure.com/)中，以管理员身份登录。
2. 转到 Active Directory，并选择你要在其中启用应用程序代理的目录。

	![Active Directory - 图标](./media/active-directory-application-proxy-enable/ad_icon.png)

3. 从目录页中选择“配置”，并向下滚动至“应用程序代理”。
4. 将“为此目录启用应用程序代理服务”切换至“已启用”状态。

	![启用应用程序代理](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. 选择“立即下载”。随即转到“Azure AD 应用程序代理连接器下载”。阅读并接受许可条款，然后单击“下载”为应用程序代理连接器保存 Windows Installer 文件 (.exe)。

## 步骤 2： 安装并注册连接器
1. 在根据先决条件准备的服务器上运行 **AADApplicationProxyConnectorInstaller.exe**。
2. 按照向导中的说明进行安装。
3. 在安装期间，系统将提示你将连接器注册到 Azure AD 租户的应用程序代理。

  - 提供你的 Azure AD 全局管理员凭据。全局管理员租户可能不同于 Microsoft Azure 凭据。
  - 确保注册连接器的管理员在你启用应用程序代理服务的目录中。例如，如果租户域为 contoso.com，则管理员应该为 admin@contoso.com 或该域上的任何其他别名。
  - 如果在你要安装 Azure AD 连接器的服务器上将“IE 增强的安全配置”设为“打开”，则可能无法显示注册屏幕。请按照错误消息中的说明来允许访问。确保 Internet Explorer 增强的安全性已关闭。
  - 如果连接器注册不成功，请参阅[排查应用程序代理问题](active-directory-application-proxy-troubleshoot.md)。

4. 安装完成后，服务器上会添加两个新的服务：

 	- **Microsoft AAD 应用程序代理连接器**将启用连接
	- **Microsoft AAD 应用程序代理连接器更新程序**是一项自动更新服务，它会定期检查连接器新版本并根据需要更新连接器。

	![应用代理连接器服务 - 屏幕截图](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. 在安装窗口中单击“完成”。

现在，你可以[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)了。

为了获得高可用性，应该部署至少两个连接器。若要部署更多连接器，请重复上面的步骤 2 和 3。每个连接器必须分开注册。

如果要卸载连接器，请先卸载连接器服务和更新程序服务。重新启动计算机以完全删除该服务。


## 后续步骤

- [使用应用程序代理发布应用程序](/documentation/articles/active-directory-application-proxy-publish/)
- [使用你自己的域名发布应用程序](/documentation/articles/active-directory-application-proxy-custom-domains/)
- [启用单一登录](/documentation/articles/active-directory-application-proxy-sso-using-kcd/)
- [解决使用应用程序代理时遇到的问题](/documentation/articles/active-directory-application-proxy-troubleshoot/)

有关最新新闻和更新，请参阅[应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)

<!----HONumber=Mooncake_0808_2016-->