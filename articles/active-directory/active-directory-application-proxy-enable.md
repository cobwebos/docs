<properties
	pageTitle="启用 Azure AD 应用程序代理 | Microsoft Azure"
	description="介绍如何使用 Azure AD 应用程序代理。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="04/12/2016"
	wacn.date=""/>

# 启用 Azure AD 应用程序代理
> [AZURE.NOTE] 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。有关详细信息，请参阅 [Azure Active Directory 版本](/documentation/articles/active-directory-editions)。

使用 Microsoft Azure Active Directory 应用程序代理可以在专用网络内部发布应用程序（例如 SharePoint 站点、Outlook Web Access 和基于 IIS 的应用），并为你网络之外的用户提供安全访问。员工可以从家中他们自己的设备登录到你的应用，并通过此基于云的代理进行身份验证。

应用程序代理的工作原理是通过在网络内部安装一个名为“连接器”的精简 Windows 服务。该连接器会维护从你的网络到代理服务的出站连接。当用户访问已发布的应用程序时，代理会使用此连接提供对应用程序的访问。

本文将指导你在 Azure AD 中为云目录启用 Microsoft Azure AD 应用程序代理，在专用网络上安装应用程序代理连接器，并将连接器注册到 Microsoft Azure AD 租户订阅。

## 应用程序代理先决条件
在启用和使用应用程序代理服务之前，你需要：

- Microsoft Azure AD [基本或高级版订阅](/documentation/articles/active-directory-editions)以及你是全局管理员的 Azure AD 目录。
- 可以在应用程序代理连接器上安装的、运行 Windows Server 2012 R2 或 Windows 8.1 版或更高版本的服务器。该服务器必须能够将 HTTPS 请求发送到云中的应用程序代理服务，且必须已与你打算发布的应用程序建立 HTTPS 连接。
- 如果防火墙位于路径中，请执行以下操作：确保打开防火墙，以允许从连接器向应用程序代理发出的 HTTPS (TCP) 请求。连接器将连同属于高级域的子域一起使用这些端口：msappproxy.net 和 servicebus.windows.net。确保打开以下用于**出站**流量的**所有**端口：

端口号 | 说明
--- | ---
80 | 启用出站 HTTP 流量以进行安全验证。
443 | 针对 Azure AD 启用用户身份验证（只有连接器注册过程才需要）
10100 - 10120 | 启用发送回代理的 LOB HTTP 响应
9352、5671 | 为传入请求启用连接器到 Azure 服务之间的通信。
9350 | 可选。提高传入请求的性能。
8080 | 启用连接器启动序列，以及启用连接器自动更新
9090 | 启用连接器注册（只有连接器注册过程才需要）
9091 | 启用连接器信任证书自动续订
 
如果你的防火墙根据发起用户强制实施流量，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。此外，请确保为 NT Authority\\System 启用端口 8080。


## 步骤 1：在 Azure AD 中启用应用程序代理
1. 在 Azure 经典门户中，以管理员身份进行登录。
2. 转到 Active Directory，并选择你要在其中启用应用程序代理的目录。
3. 单击“配置”，向下滚动到“应用程序代理”，然后将“为此目录启用应用程序代理服务”切换到“已启用”。

	![启用应用程序代理](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

4. 单击屏幕底部的“立即下载”。然后你将转到下载页。阅读并接受许可条款，然后单击“下载”以保存应用程序代理连接器的 Windows 安装程序文件 (.exe)。

## 步骤 2： 安装并注册连接器
1. 在你准备好的服务器上运行 `AADApplicationProxyConnectorInstaller.exe`（请参阅上面的“应用程序代理先决条件”）。
2. 按照向导中的说明进行安装。
3. 在安装期间，系统将提示你将连接器注册到 Azure AD 租户的应用程序代理。

  - 提供你的 Azure AD 全局管理员凭据。全局管理员租户可能不同于 Microsoft Azure 凭据。
  - 请确保注册连接器的管理员在你启用应用程序代理服务时所在的同一目录中，例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的任何其他别名。
  - 如果在你要安装 Azure AD 连接器的服务器上将“IE 增强的安全配置”设为“打开”，则可能无法显示注册屏幕。如果发生这种情况，请按照错误消息中的说明来允许访问。确保 Internet Explorer 增强的安全性已关闭。
  - 如果连接器注册不成功，请参阅[应用程序代理故障排除](active-directory-application-proxy-troubleshoot.md)。  

4. 安装完成后，服务器上会添加两个新的服务，如下所示。其中包括一个用于启用连接的连接器服务，以及一个定期检查连接器新版本并根据需要更新连接器的自动更新服务。在安装窗口中单击“完成”以完成安装

	![应用程序代理连接器服务](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. 现在，你可以[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)了。

为了获得高可用性，必须至少部署一个额外的连接器。若要部署附加连接器，请重复上面的步骤 2 和步骤 3。每个连接器必须分开注册。

如果要卸载该连接器，请先卸载连接器服务和更新程序服务，然后确保重新启动计算机以完全删除该服务。


## 另请参阅
应用程序代理还有其他用途：

- [使用应用程序代理发布应用程序](/documentation/articles/active-directory-application-proxy-publish)
- [使用你自己的域名发布应用程序](/documentation/articles/active-directory-application-proxy-custom-domains)
- [启用单一登录](/documentation/articles/active-directory-application-proxy-sso-using-kcd)
- [启用条件性访问](/documentation/articles/active-directory-application-proxy-conditional-access)
- [使用声明感知应用程序](/documentation/articles/active-directory-application-proxy-claims-aware-apps)
- [解决使用应用程序代理时遇到的问题](/documentation/articles/active-directory-application-proxy-troubleshoot)

## 了解有关应用程序代理的详细信息
- [参阅应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)
- [观看第 9 频道上的视频！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他资源
- [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)
* [以组织身份注册 Azure](/documentation/articles/sign-up-organization)
* [Azure 标识](/documentation/articles/fundamentals-identity)
- [使用应用程序代理发布应用程序](/documentation/articles/active-directory-application-proxy-publish)

<!---HONumber=Mooncake_0509_2016-->