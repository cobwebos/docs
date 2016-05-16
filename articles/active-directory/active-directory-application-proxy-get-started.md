<properties
	pageTitle="如何提供对本地应用的安全远程访问"
	description="介绍如何使用 Azure AD 应用程序代理来提供对本地应用的安全远程访问。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="04/12/2016"
	wacn.date=""/>

# 如何提供对本地应用程序的安全远程访问

> [AZURE.NOTE] 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

你想为拥有各种设备（托管和非托管；平板电脑、智能手机和笔记本电脑）的远程用户提供访问权限。但是，为大量的资源提供安全访问可能很复杂。近年来，反向代理是提供安全远程访问的一种常见方式，但它们必须在难以保护且难以提供高度可用性的防火墙后面。

## 云中的安全远程访问
在现代云环境中，我们使用 Microsoft Azure Active Directory (AD) 中的应用程序代理将远程访问提升到一个新的层次。应用程序代理是 Azure AD 以服务形式提供的一项功能，这意味着它的部署与使用都非常简单。它集成了 Office 365 使用的相同标识平台，即 Azure AD。

## 什么是 Azure Active Directory 应用程序代理？
应用程序代理针对本地托管的 Web 应用程序（例如 SharePoint 站点和 Outlook Web Access）提供单一登录 (SSO) 和安全远程访问。本地 Web 应用程序的访问方式现在与 Azure Active Directory 中的 SaaS 应用访问方式相同，不需要 VPN，也不需要更改网络基础结构。你可以利用应用程序代理发布应用程序，之后员工可以从家中或通过他们自己的设备登录到你的应用，并通过此基于云的代理进行身份验证。

## 工作原理
### 启用访问
应用程序代理的工作原理是通过在网络内部安装一个名为“连接器”的精简 Windows Server 服务。连接器不一定需要开放任何入站端口，并且不需要在外围网络中放置任何内容。如果应用有大量的流量，你可以添加更多连接器，该服务将负责负载平衡。连接器是无状态的，并且会根据需要从云中提取所有内容。当用户从远程访问任何设备上的应用程序时，他们将经过 Azure Active Directory 的身份验证，并获取应用程序的访问权限。

### 单一登录
Azure AD 应用程序代理针对使用集成 Windows 身份验证 (IWA) 的应用程序或声明感知应用程序提供单一登录。如果你的应用程序使用 IWA，则应用程序代理将模拟使用 Kerberos 约束委派的用户来提供 SSO。如果你有信任 Azure Active Directory 的声明感知应用程序，则可以实现 SSO，因为用户已经由 Azure AD 进行身份验证。

## 如何入门
请确保你有 Azure AD 基本或高级版订阅，并且是 Azure AD 目录的全局管理员。你还需要拥有 Azure AD 基本或高级版的许可证才能让目录管理员和用户访问应用。有关详细信息，请参阅此文。

### 开始启用对本地应用程序的远程访问
设置应用程序代理由两个步骤组成：

1. [启用应用程序代理并配置连接器](/documentation/articles/active-directory-application-proxy-enable)<br>
2. [发布应用程序](/documentation/articles/active-directory-application-proxy-publish) — 使用快速方便的向导发布本地应用，然后即可远程访问。

## 后续步骤
应用程序代理还有其他用途：


- [使用你自己的域名发布应用程序](https://msdn.microsoft.com/zh-cn/library/azure/mt210927.aspx)
- [启用单一登录](https://msdn.microsoft.com/zh-cn/library/azure/dn879065.aspx)
- [使用声明感知应用程序](https://msdn.microsoft.com/zh-cn/library/azure/mt210926.aspx)
- [启用条件性访问](https://msdn.microsoft.com/zh-cn/library/azure/dn931796.aspx)


### 了解有关应用程序代理的详细信息
- [在此处查看我们的联机帮助](https://msdn.microsoft.com/zh-cn/library/azure/dn768219.aspx)
- [参阅应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)
- [观看第 9 频道上的视频！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他资源
- [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)
* [以组织身份注册 Azure](/documentation/articles/sign-up-organization)
* [Azure 标识](/documentation/articles/fundamentals-identity)

<!---HONumber=Mooncake_0509_2016-->