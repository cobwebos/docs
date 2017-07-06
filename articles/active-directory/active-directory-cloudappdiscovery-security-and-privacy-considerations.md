---
title: "Cloud App Discovery 的安全和隐私注意事项 | Microsoft Docs"
description: "本主题介绍与 Cloud App Discovery 相关的安全和隐私注意事项。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: b621a1716b731c99f9ad54d2e29006fb7bddadbb
ms.openlocfilehash: 5c4ab6e08c8f1af89ea80ac7f4d58d82ee931ec9
ms.contentlocale: zh-cn
ms.lasthandoff: 01/12/2017


---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Cloud App Discovery 的安全和隐私注意事项
Microsoft 致力于保护你的隐私和数据安全，同时提供软件和服务来帮助你管理组织的安全性。  
我们认识到，当你授信他人访问你的数据时，这种信任需要极高的安全工程投资和专业知识来提供支持。
从安全软件开发生命周期实践到服务运营，Microsoft 都严格遵守法规与安全准则。  
保护数据是 Microsoft 的头等大事。

本主题介绍如何在 Azure Active Directory Cloud App Discovery 中收集、处理和保护数据

## <a name="overview"></a>概述
Cloud App Discovery 是 Azure AD 的一项功能，在 Microsoft Azure 中托管。  
Cloud App Discovery 终结点代理用于从 IT 托管的计算机收集应用程序发现数据。  
收集的数据通过加密通道安全地发送到 Azure AD Cloud App Discovery 服务。  
然后，组织的 Cloud App Discovery 数据会显示在 Azure 门户中。 

![云应用程序发现的工作原理](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

以下部分以信息流为主，介绍如何从你的组织将信息移到 Cloud App Discovery 服务并最终移到 Cloud App Discovery 门户时为其提供保护。

## <a name="collecting-data-from-your-organization"></a>从你的组织收集数据
若要使用 Azure Active Directory 的 Cloud App Discovery 功能来了解组织中的员工使用的应用程序，你需要首先将 Azure AD Cloud App Discovery 终结点代理部署到你的组织中的计算机。

Azure Active Directory 租户（或其代理）的管理员可从 Azure 门户下载代理安装包。 这些代理可以手动安装或使用 SCCM 或组策略安装在组织中的多台计算机上。

有关部署选项的进一步说明，请参阅 [Cloud App Discovery Group Policy Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)（Cloud App Discovery 组策略部署指南）。


### <a name="data-collected-by-the-agent"></a>代理收集的数据
在建立与 Web 应用程序的连接时，代理将收集下表中概述的信息。 该信息仅收集用于管理员已为发现配置的这些应用程序。  
以通过 Microsoft [Azure 门户](https://portal.azure.com/)中的“Cloud App Discovery”边栏选项卡，在“设置”->“数据收集”->“应用集合列表”下面，编辑代理监视的云应用列表。 有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）


**信息类别**：用户信息  
**说明**：  
已对目标 Web 应用程序发出请求的进程的 Windows 用户名（例如：DOMAIN\username），以及用户的 Windows 安全标识符 (SID)。

**信息类别**：进程信息  
**说明**：  
已对目标 Web 应用程序发出请求的进程的名称（例如：“iexplore.exe”）

**信息类别**：计算机信息  
**说明**：  
已安装代理的计算机的 NetBIOS 名称。

**信息类别**：应用流量信息  
**说明**： 

以下连接信息：

* 源（本地计算机）和目标 IP 地址和端口号
* 组织的公共 IP 地址，请求通过该地址发出。
* 请求的时间
* 发送和接收的流量
* IP 版本（4 或 6）
* 仅限 TLS 连接：来自服务器名称指示扩展或服务器证书的目标主机名。

以下 HTTP 信息：

* 方法（GET、POST 等）
* 协议（HTTP/1.1 等）
* 用户代理字符串
* 主机名
* 目标 URI（不包括查询字符串）
* 内容类型信息
* 引用站点 URL 信息（不包括查询字符串）

> [!NOTE]
> 以上 HTTP 信息收集用于所有的非加密连接。
> 对于 TLS 连接，仅在门户中打开“深度检测”设置时，才捕获此信息。 默认情况下设置为“打开”。
> 有关更多详细信息，请参阅下文以及 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
> 
> 

代理除了收集有关网络活动的数据以外，还收集有关软件和硬件配置的匿名信息、错误报告以及有关代理用法的信息。


### <a name="how-the-agent-works"></a>代理工作原理
代理安装包括两个组件：

* 用户模式组件
* 内核模式驱动程序组件（Windows 筛选平台驱动程序）

首次安装代理时，它将在计算机上存储特定于计算机的受信任的证书，然后用该证书来建立与 Cloud App Discovery 服务的安全连接。  
代理会通过此安全连接从 Cloud App Discovery 服务定期检索策略配置。  
该策略包括有关要监视的云应用程序以及是否应启用自动更新等的信息。

在计算机的 Internet Explorer 和 Chrome 中发送和接收 Web 流量时，Cloud App Discovery 代理分析该流量并提取相关的元数据（请参阅上面的**代理收集的数据**部分）。  
代理每一分钟通过加密通道将收集的元数据上载到 Cloud App Discovery 服务。

驱动程序组件会截获加密的流量，并将自身插入到加密流中。 有关详细信息，请参阅下面的**拦截来自加密连接的数据（深度检测）**部分。

### <a name="respecting-user-privacy"></a>尊重用户隐私
我们的目标是为管理员提供工具，用于根据组织的需要在详细的应用程序用法和用户隐私之间设置平衡。 最后，我们将在门户的“设置”页中提供以下旋钮：

* **数据收集**：管理员可以选择指定要获取其发现数据的应用程序或应用程序类别。
* **深度检测**：管理员可以选择指定代理是否收集 SSL/TLS 连接的 HTTP 流量（即**深度检测**）。 下一部分将会详述此技术。
* **同意选项**：管理员可以使用 Cloud App Discovery 门户选择是否要让用户知道代理将要收集数据，以及在代理开始收集用户数据之前是否需要获得用户同意。

Cloud App Discovery 终结点代理只收集上面**代理收集的数据**中所述的信息。

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>拦截来自加密连接的数据（深度检测）
如先前所述，管理员可以设置代理，以监视来自加密连接的数据（“深度检测”）。 TLS（[传输层安全性](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)）是目前在 Internet 上最常使用的协议之一。 通过使用 TLS 加密通信，客户端可以创建与 Web 服务器之间的安全且私密的通信通道；TLS 为传递身份验证凭据提供基本保护，防止敏感信息遭到泄漏。

尽管通过 TLS 提供的端到端安全加密通道也能实现重要的安全和隐私保护，但是协议通常被恶意用户的滥用。 事实上，人们经常将 TLS 称作“绕过防火墙的通用协议”。 问题的根源在于应用程序层数据是使用 SSL 加密的，因此大多数防火墙无法检查 TLS 通信。 知道这一点后，攻击者通常利用 TLS 将恶意负载传递给确信即使是最高程度的智能应用程序层防火墙，对于 TLS 也完全视而不见，并且必须只在主机之间中继 TLS 通信的用户。 最终用户经常利用 TLS 来绕过其企业防火墙和代理服务器强制实施的访问控制，使用它连接到公共代理，以及通过可能遭到策略阻止的防火墙来打开非 TLS 协议的隧道。

深度检测允许 Cloud App Discovery 代理充当受信任的中间人。 当发出访问受 HTTPS 保护资源的客户端请求时，终结点代理驱动程序将拦截连接，并创建与目标服务器的新连接，以代表客户端检索其 SSL 证书。 然后，代理验证是否可以信任该证书（通过检查证书是否未被吊销和执行其他证书检查）；如果这些检查通过了检查，则终结点代理将复制服务器证书中的信息，并使用该信息创建自身的服务器证书（称为拦截证书）。 终结点代理使用安装在 Windows 可信证书存储区中的根证书来为拦截证书动态签名。 此自签名根证书标记为不可导出，是颁发给管理员的 ACL。 该证书旨在永久保留在创建它的计算机上。 最终用户的客户端应用程序收到拦截证书后，由于可以成功验证直到根证书的整个证书链，因此将信任该证书。 从最终用户的观点来看，此过程基本上是透明的，但有以下几点注意事项。

通过启用深度检测，Cloud App Discovery 终结点代理可以解密并检查 TLS 加密的通信，使服务能够减少干扰，并提供有关加密云应用使用方式的深入见解。

#### <a name="a-word-of-caution"></a>注意事项
启用深度检测之前，强烈建议你与法律和人力资源部门沟通想法，并获取他们的同意。 原因很简单，检查用户的私人加密通信是非常敏感的话题。 在生产环境中展开深度检测之前，请确保更新企业的安全与可接受用法策略，指明将要检查加密的通信。 如果你将 Cloud App Discovery 配置为监视用户，则可能还需要通知用户，并排除视为机密的站点（例如，银行和医疗站点）。 如前所述，管理员可以使用 Cloud App Discovery 门户选择是否要让用户知道代理将要收集数据，以及在代理开始收集用户数据之前是否需要获得用户同意。

### <a name="known-issues-and-drawbacks"></a>已知问题和缺点
下面是 TLS 拦截可能对用户体验造成影响的一些场合：

* 扩展验证 (EV) 证书将 Web 浏览器的地址栏标为绿色，在你访问受信任网站时充当视觉提示。 TLS 检查无法复制颁发给客户端的证书中的 EV，因此使用 EV 证书的网站尽管可以正常运行，但地址栏不显示为绿色。　  
* 公钥绑定（也称为证书绑定）旨在帮助保护用户，以免其受到中间人攻击和恶意证书颁发机构的攻击。 当绑定站点的根证书与已知正常 CA 的某个根证书不匹配时，浏览器将拒绝连接并返回错误。 事实上，由于 TLS 拦截是一种中间人活动，因此这些连接将会失败。
* 如果用户单击浏览器地址栏中的锁形图标来检查站点信息，看到的证书链不是用来为网站证书签名的证书颁发机构，而是 Windows 可信证书存储区。

为了减少这些问题的出现频率，我们将持续跟踪已知使用扩展验证或公钥绑定的云服务和客户端应用程序，并指示终结点代理避免拦截受影响的连接。 但是，即使在这种情况下，你仍会收到有关这些云应用的用法以及所传输的数据量的报告，但由于未深度检测，因此不提供应用使用方式的详细信息。

## <a name="sending-data-to-cloud-app-discovery"></a>将数据发送到 Cloud App Discovery
元数据一旦由代理收集，就会在计算机上缓存一分钟，或直到缓存的数据达到 5MB 的大小。 然后，这些数据会被压缩，并通过安全连接发送到 Cloud App Discovery 服务。

如果出于任何原因，代理无法与 Cloud App Discovery 服务进行通信，则收集的元数据存储在只能由计算机上的特权用户（如管理员组）访问的本地文件缓存中。  
代理自动尝试重新发送缓存的元数据，直到 Cloud App Discovery 服务成功接收到元数据为止。

## <a name="receiving-the-data-at-the-service-end"></a>接收服务端的数据
代理使用上述计算机特定的客户端身份验证证书对 Cloud App Discovery 服务进行身份验证，并通过加密通道转发数据。  
Cloud App Discovery 服务的分析管道可通过在分析管道的各个阶段对元数据进行逻辑分区来为每个客户分别处理这些元数据。
根据经过分析的元数据，可在门户中生成各种报告。

未处理的元数据和经过分析的元数据最多存储 180 天。 此外，客户可以选择将经过分析的元数据捕获到其选择的 Azure Blob 存储帐户中。
这对脱机分析元数据以及延长数据保留时间很有用。

## <a name="accessing-the-data-using-the-azure-portal"></a>使用 Azure 门户访问数据
为了使收集的元数据受到保护，默认情况下，只有租户的全局管理员才有权在 Azure 门户中访问 Cloud App Discovery 功能。  
但是，管理员可以选择向其他用户或组委托此访问权限。

> [!NOTE]
> 有关更多详细信息，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）
> 
> 


任何在门户中访问数据的用户，都必须使用 Azure AD Premium 许可证进行许可。

## <a name="additional-resources"></a>其他资源
* [如何发现本组织中使用的未经许可的云应用](active-directory-cloudappdiscovery-whatis.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)


