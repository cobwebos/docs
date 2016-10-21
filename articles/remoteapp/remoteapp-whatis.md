<properties 
	pageTitle="什么是 Azure RemoteApp？| Microsoft Azure" 
	description="了解如何通过 Azure RemoteApp 将应用和资源共享给任何设备。" 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/15/2016" 
	ms.author="elizapo"/>

# 什么是 Azure RemoteApp？

> [AZURE.IMPORTANT]
正在中断 Azure RemoteApp。阅读[公告](https://go.microsoft.com/fwlink/?linkid=821148)了解详细信息。

Azure RemoteApp 将远程桌面服务支持的本地 Microsoft RemoteApp 程序的功能引入到 Azure 中。Azure RemoteApp 有助于从多个不同的用户设备对应用程序提供安全的远程访问。基本上，Azure RemoteApp 在云中托管非永久终端服务器会话，可以使用它们并与用户共享。

借助 Azure RemoteApp，可以与几乎任何设备上的用户共享应用和资源。我们在云中托管应用，这意味着我们需要管理硬件和缩放以满足用户需求。你只需要上传要共享的应用，然后让用户使用这些应用。[用户需保留其自己的设备](remoteapp-clients.md)，而你通过 Azure 门户管理所有内容。甚至可以选择使用公司凭据，确保应用和数据的安全。

继续阅读了解有关 Azure RemoteApp 的详细信息，或者，如果我们已经说服了你，[欢迎立即试用](https://azure.microsoft.com/services/remoteapp/)。

有任何关于 Azure RemoteApp 的问题？ 查看[常见问题解答](remoteapp-faq.md)。

Azure RemoteApp 是 [Microsoft 虚拟桌面基础结构](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)的一部分。

**新增内容！** 想要详细了解 Azure RemoteApp？ 或已准备好大规模验证 Azure RemoteApp？ 加入每周的[专家咨询网络研讨会](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)。

## Azure RemoteApp 集合
有两种类型的 [Azure RemoteApp 集合](remoteapp-collections.md)：


- **云集合**托管于云中并在其中存储程序数据。用户可通过使用其 Microsoft 帐户或与 Azure Active Directory 同步或联合的企业凭据登录来访问应用。

	要共享的应用程序不需要连接到公司专用网络的任何资源（例如，通过 VPN 设备）时，请选择云集合。如果应用程序使用 Internet、OneDrive 或 Azure 上的资源，云集合便可用于这种情况。它也是创建最快的。

- **混合集合**托管于 Azure 云，它不仅在 Azure 云中存储数据，而且允许用户访问本地网络中存储的数据和资源。用户可通过使用其与 Azure Active Directory 同步或联合的企业凭据登录来访问应用。

	如果需要连接到公司专用网络上的资源，请选择混合集合。例如，如果应用程序需要访问以下项之一：

	- 位于 Intranet 上的文件服务器
	- Quicken
	- 防火墙后面的数据库

	这通常更适用于专用网络上具有大量资源，不能移到云中的大型企业。

不同的集合具有不同的选项（包括网络），因此需弄清[哪个集合](remoteapp-collections.md)最适合自己。


### 更新集合
混合集合和云集合之间的主要区别之一是如何处理软件更新。对于使用预安装的 Office 365 ProPlus 或 Office 2013 映像的云集合，不必担心任何更新。服务进行自身维护并不断更新应用和操作系统。

对于混合集合，以及使用自定义模板映像的云集合，用户负责维护映像和应用。对于已加入域的映像，可以通过使用 Windows 更新、组策略或 System Center 等工具控制更新。

更新自定义模板映像后，将新的映像上传到 Azure 云，然后更新集合，使用新映像。（可以从 Azure RemoteApp“快速启动”页或仪表板执行此操作。）

请参阅[更新集合](remoteapp-update.md)了解详细信息。

## 支持的 RemoteApp 客户端
适用于 Windows 和 Windows RT 的 RemoteApp 客户端应用，以及适用于 Mac、iOS 和 Android 的 Microsoft 远程桌面应用都支持 Azure RemoteApp。用户可以在其移动设备或计算设备上使用这些应用访问新的 Azure RemoteApp 程序。

请参阅[访问 Azure RemoteApp 中的应用](remoteapp-clients.md)了解有关客户端的详细信息。

## 后续步骤
开始！ 试试看！ 这些文章有助于了解 Azure RemoteApp:

- [ Azure RemoteApp 需要哪种集合？](remoteapp-collections.md)
- [创建 Azure RemoteApp 映像](remoteapp-imageoptions.md)
- [如何创建 Azure RemoteApp 的云集合](remoteapp-create-cloud-deployment.md)
- [如何创建 Azure RemoteApp 的混合集合](remoteapp-create-hybrid-deployment.md)
- [如何在 Azure RemoteApp 中进行授权？](remoteapp-licensing.md)
- [Azure RemoteApp 的最佳实践](remoteapp-bestpractices.md)
- [Azure RemoteApp 常见问题解答](remoteapp-faq.md)
 

### 请帮助我们改进，以便为你提供更好的帮助和支持 
是否知道除了给这篇文章评级和在下面发表评论，还可以对文章本身进行更改？ 缺了什么？ 出现了错误？ 是否编写了令人困惑的内容？ 向上滚动并单击“在 GitHub 上编辑”或“编辑”进行更改 - 更改的内容会发送给我们进行审阅，一旦签核，便可以在此处看到这些更改和改进。

<!---HONumber=AcomDC_0921_2016-->