<properties 
	pageTitle="Cloud App Discovery 的安全和隐私注意事项" 
	description="本主题介绍与 Cloud App Discovery 相关的安全和隐私注意事项。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.date="07/23/2015" 
	wacn.date=""/>

# Cloud App Discovery 的安全和隐私注意事项

Microsoft 致力于保护你的隐私和数据安全，同时提供软件和服务来帮助你管理组织的安全性。<br>
我们认识到，当你授信他人访问你的数据时，这种信任需要极高的安全工程投资和专业知识来提供支持。
从安全软件开发生命周期实践到服务运营，Microsoft 都严格遵守法规与安全准则。<br>
保护数据是 Microsoft 的头等大事。

本主题介绍如何在 Azure Active Directory Cloud App Discovery 中收集、处理和保护数据




##概述

Cloud App Discovery 是 Azure AD 的一项功能，在 Microsoft Azure 中托管。<br>
Cloud App Discovery 终结点代理用于从 IT 托管的计算机收集应用程序发现数据。<br>
收集的数据通过加密通道安全地发送到 Azure AD Cloud App Discovery 服务。<br>
然后，组织的 Cloud App Discovery 数据会显示在 Azure 门户中。


<center>![Cloud App Discovery 工作原理](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


以下部分以信息流为主，介绍如何从你的组织将信息移到 Cloud App Discovery 服务并最终移到 Cloud App Discovery 门户时为其提供保护。



## 从你的组织收集数据

若要使用 Azure Active Directory 的 Cloud App Discovery 功能来了解组织中的员工使用的应用程序，你需要首先将 Azure AD Cloud App Discovery 终结点代理部署到你的组织中的计算机。

Azure Active Directory 租户（或其代理）的管理员可从 Azure 门户下载代理安装包。这些代理可以手动安装或使用 SCCM 或组策略安装在组织中的多台计算机上。

有关部署选项的进一步说明，请参阅 [Cloud App Discovery Group Policy Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)（Cloud App Discovery 组策略部署指南）。


### 代理收集的数据

在建立与网站的连接时，代理将收集下表中概述的信息。该信息仅收集用于管理员已为应用程序发现配置的这些应用程序。<br>
选择收集其元数据的应用程序列表可在门户中的“设置”选项卡下配置。



> [AZURE.NOTE] 有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
 
**信息类别**：用户信息<br>
**说明**：<br>
已对目标网站发出请求的进程的 Windows 用户名（例如：DOMAIN\\username），以及用户的 Windows 安全标识符 (SID)。


**信息类别**：进程信息<br>
**说明**：<br>
已对目标网站发出请求的进程的名称（例如：“iexplore.exe”）

**信息类别**：计算机信息<br>
**说明**：<br>
代理安装到的计算机 NetBIOS 名称。

**信息类别**：应用流量信息<br>
**说明**：<br>

以下连接信息：

- 源（本地计算机）和目标 IP 地址和端口号 

- 组织的公共 IP 地址，请求通过该地址发出。

- 请求的时间

- 发送和接收的流量

- IP 版本（4 或 6）

- 仅限 TLS 连接：来自服务器名称指示扩展或服务器证书的目标主机名。

以下 HTTP 信息：

- 方法（GET、POST 等） 

- 协议（HTTP/1.1 等）

- 用户代理字符串

- 主机名

- 目标 URI（不包括查询字符串）

- 内容类型信息

- 引用站点 URL 信息（不包括查询字符串）



> [AZURE.NOTE] 以上 HTTP 信息收集用于所有的非加密连接。对于 TLS 连接，仅在门户中打开“深度检测”设置时，才捕获此信息。默认情况下设置为“打开”。
有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
 

 
### 代理工作原理

代理安装包括两个组件：

- 用户模式组件

- 内核模式驱动程序组件（Windows 筛选平台驱动程序）



首次安装代理时，它将在计算机上存储特定于计算机的受信任的证书，然后用该证书来建立与 Cloud App Discovery 服务的安全连接。<br> 
代理会通过此安全连接从 Cloud App Discovery 服务定期检索策略配置。<br> 
该策略包括有关要监视的云应用程序以及是否应启用自动更新等的信息。

在计算机上收发 Web 流量时， Cloud App Discovery 会分析流量，并提取相关元数据（请参阅上表）。<br> 
代理每一分钟会通过加密通道将收集的元数据上载到 Cloud App Discovery 服务。

对于加密连接，还需要执行一个步骤，以便提高收集的数据的准确性。<br> 
这称为深度检测。<br> 
驱动程序组件会截获加密的流量，并将自身插入到加密流中。 
为此，它会在计算机上创建自签名根证书，这将导致客户端应用程序信任 Cloud App Discovery 代理。此自签名根证书标记为不可导出，是颁发给管理员的 ACL。该证书旨在永久保留在创建它的计算机上。


### 尊重用户隐私

我们的目标是为管理员提供工具，用于根据组织的需要在详细的应用程序用法和用户隐私之间设置平衡。最后，我们将在门户的“设置”页中提供以下旋钮：

- **数据收集**：管理员可以选择指定要获取其发现数据的应用程序或应用程序类别。


- **深度检测**：管理员可以选择指定代理是否收集 SSL/TLS 连接的 HTTP 流量。



Cloud App Discovery 终结点代理只收集上表中所述的信息。



> [AZURE.NOTE] 有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
 



## 将数据发送到 Cloud App Discovery

元数据一旦由代理收集，就会在计算机上缓存一分钟，或直到缓存的数据达到 5MB 的大小。然后，这些数据会被压缩，并通过安全连接发送到 Cloud App Discovery 服务。

如果出于任何原因，代理无法与 Cloud App Discovery 服务进行通信，则收集的元数据存储在只能由计算机上的特权用户（如管理员组）访问的本地文件缓存中。<br> 
代理会自动尝试重新发送缓存的元数据，直到 Cloud App Discovery 服务成功接收到它为止。



## 接收服务端的数据

代理使用上述计算机特定的客户端身份验证证书对 Cloud App Discovery 服务进行身份验证，并通过加密通道转发数据。<br> 
Cloud App Discovery 服务的分析管道可通过在分析管道的各个阶段对元数据进行逻辑分区来为每个客户分别处理这些元数据。根据经过分析的元数据，可在门户中生成各种报告。

未处理的元数据和经过分析的元数据最多存储 180 天。此外，客户可以选择将经过分析的元数据捕获到其选择的 Azure Blob 存储帐户中。这对脱机分析元数据以及延长数据保留时间很有用。

## 使用 Azure 门户访问数据

为了使收集的元数据受到保护，默认情况下，只有租户的全局管理员才有权在 Azure 门户中访问 Cloud App Discovery 功能。<br> 
但是，管理员可以选择向其他用户或组委派此访问权限。



> [AZURE.NOTE] 有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
 


任何在门户中访问数据的用户，都必须使用 Azure AD Premium 许可证进行许可。



**其他资源**


* [如何发现本组织中使用的未经认可的云应用](/documentation/articles/active-directory-cloudappdiscovery-whatis) 



<!---HONumber=Mooncake_0411_2016-->