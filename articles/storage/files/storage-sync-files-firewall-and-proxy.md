---
title: Azure 文件同步本地防火墙和代理设置 | Microsoft Docs
description: Azure 文件同步本地网络配置
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: fauhse
ms.component: files
ms.openlocfilehash: 44bfdd192f846b710e378b1f00799eda304cec1e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522758"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 文件同步代理和防火墙设置
Azure 文件同步可以将本地服务器连接到 Azure 文件，启用多站点同步和云分层功能。 因此，本地服务器必须连接到 Internet。 IT 管理员需确定服务器访问 Azure 云服务的最佳路径。

本文介绍需要完成哪些具体的要求和选项才能成功地将服务器安全地连接到 Azure 文件同步。

> [!Important]
> Azure 文件同步尚不支持对存储帐户启用防火墙和虚拟网络。

## <a name="overview"></a>概述
Azure 文件同步在 Windows Server、Azure 文件共享和多项其他的 Azure 服务之间充当业务流程服务，用于同步同步组中所述的数据。 需将服务器配置为与以下 Azure 服务通信，确保 Azure 文件同步正常工作：

- Azure 存储
- Azure 文件同步
- Azure 资源管理器
- 身份验证服务

> [!Note]  
> Windows Server 上的 Azure 文件同步代理会启动到云服务的所有请求，因此从防火墙的角度来看，只需考虑出站流量。 <br /> 没有任何 Azure 服务会启动到 Azure 文件同步代理的连接。

## <a name="ports"></a>端口
Azure 文件同步以独占方式通过 HTTPS 移动文件数据和元数据，要求端口 443 对外开放。
因此，所有流量都是加密的。

## <a name="networks-and-special-connections-to-azure"></a>网络以及到 Azure 的特殊连接
对于到 Azure 的特殊通道（例如 [ExpressRoute](../../expressroute/expressroute-introduction.md) 等），Azure 文件同步代理没有任何要求。

Azure 文件同步会通过任何可用方式来访问 Azure，自动适应各种网络特征（例如带宽、延迟）并提供进行微调所需的管理员控制。 目前并没有提供所有功能。 如果需要配置特定的行为，请通过 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) 告知我们。

## <a name="proxy"></a>代理
Azure 文件同步支持特定于应用和计算机范围的代理设置。

计算机范围的代理设置对 Azure 文件同步代理来说是透明的，因为服务器的整个流量都通过该代理路由。

特定于应用的代理设置可以配置专用于 Azure 文件同步流量的代理。 代理版本 3.0.12.0 或更高版本支持特定于应用的代理设置，可以在代理安装期间或使用 Set-StorageSyncProxyConfiguration PowerShell cmdlet 进行配置。

用于配置特定于应用的代理设置的 PowerShell 命令：
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>防火墙
如前面的部分所述，端口 443 需对外开放。 可能需要进一步对通过此端口流向特定域的流量进行限制，具体取决于所在数据中心、分支或区域的策略。

下表介绍了进行通信所需的域：

| 服务 | 域 | 使用情况 |
|---------|----------------|------------------------------|
| **Azure 资源管理器** | https://management.azure.com | 包括初始服务器注册调用在内的任何用户调用（例如 PowerShell）都会转到/经过此 URL。 |
| **Azure Active Directory** | https://login.windows.net | Azure 资源管理器调用必须由经过身份验证的用户发出。 若要成功，请使用此 URL 进行用户身份验证。 |
| **Azure Active Directory** | https://graph.windows.net/ | 在部署 Azure 文件同步的过程中，将在订阅的 Azure Active Directory 中创建服务主体。 此 URL 用于该操作。 此主体用于将最小的一组权限委托给 Azure 文件同步服务。 对 Azure 文件同步进行初始设置的用户必须是经过身份验证且具有订阅所有者特权的用户。 |
| **Azure 存储** | &ast;.core.windows.net | 服务器在下载某个文件时，可以直接与存储帐户中的 Azure 文件共享通信，从而提高数据移动效率。 服务器有一个 SAS 密钥，只允许进行针对性的文件共享访问。 |
| **Azure 文件同步** | &ast;.one.microsoft.com | 在完成初始服务器注册以后，服务器会收到一个区域 URL，适用于该区域中的 Azure 文件同步服务实例。 服务器可以使用此 URL 直接且高效地与负责其同步的实例通信。 |

> [!Important]
> 如果允许流量通往 &ast;.one.microsoft.com，则可以让流量从服务器通往除同步服务之外的其他服务。 子域下还有更多可用的 Microsoft 服务。

如果 &ast;.one.microsoft.com 范围太广，则可限制服务器的通信，只允许与 Azure 文件同步服务的显式区域性实例通信。 选择哪个或哪些实例取决于向其部署和注册了服务器的存储同步服务的区域。 在下表中，该区域称为“主终结点 URL”。

出于业务连续性和灾难恢复 (BCDR) 的原因，你可能在全局冗余 (GRS) 存储帐户中指定了 Azure 文件共享。 如果是这样，在发生长时间的区域性中断时，Azure 文件共享将故障转移到配对的区域。 Azure 文件同步使用的区域配对与存储相同。 因此，如果使用 GRS 存储帐户，则需要启用附加的 URL 才能让服务器与 Azure 文件同步的配对区域通信。在下表中，此配对称为“配对区域”。 此外，还需要启用一个流量管理器配置文件 URL。 在发生故障转移时，此 URL 可确保将网络流量无缝重新路由到配对区域；在下表中，此 URL 称为“发现 URL”。

| 区域 | 主终结点 URL | 配对区域 | 发现 URL | |--------|---------------------------------------||--------||---------------------------------------| | 澳大利亚东部 | https://kailani-aue.one.microsoft.com | 澳大利亚东南部 | https://kailani-aue.one.microsoft.com | | 澳大利亚东南部 | https://kailani-aus.one.microsoft.com | 澳大利亚东部 | https://tm-kailani-aus.one.microsoft.com | | 加拿大中部 | https://kailani-cac.one.microsoft.com | 加拿大东部 | https://tm-kailani-cac.one.microsoft.com | | 加拿大东部 | https://kailani-cae.one.microsoft.com | 加拿大中部 | https://tm-kailani.cae.one.microsoft.com | | 美国中部 | https://kailani-cus.one.microsoft.com | 美国东部 2 | https://tm-kailani-cus.one.microsoft.com | | 东亚 | https://kailani11.one.microsoft.com | 东南亚 | https://tm-kailani11.one.microsoft.com | | 美国东部 | https://kailani1.one.microsoft.com | 美国西部 | https://tm-kailani1.one.microsoft.com | | 美国东部 2 | https://kailani-ess.one.microsoft.com | 美国中部 | https://tm-kailani-ess.one.microsoft.com | | 北欧 | https://kailani7.one.microsoft.com | 西欧 | https://tm-kailani7.one.microsoft.com | | 东南亚 | https://kailani10.one.microsoft.com | 东亚 | https://tm-kailani10.one.microsoft.com | | 英国南部 | https://kailani-uks.one.microsoft.com | 英国西部 | https://tm-kailani-uks.one.microsoft.com | | 英国西部 | https://kailani-ukw.one.microsoft.com | 英国南部 | https://tm-kailani-ukw.one.microsoft.com | | 西欧 | https://kailani6.one.microsoft.com | 北欧 | https://tm-kailani6.one.microsoft.com | | 美国西部 | https://kailani.one.microsoft.com | 美国东部 | https://tm-kailani.one.microsoft.com |

- 如果使用本地冗余 (LRS) 或区域冗余 (ZRS) 存储帐户，只需启用“主终结点 URL”下面列出的 URL。

- 如果使用全局冗余 (GRS) 存储帐户，请启用三个 URL。

**示例：** 在 `"West US"` 中部署存储同步服务并在其中注册自己的服务器。 在本例中，允许与服务器通信的 URL 为：

> - https://kailani.one.microsoft.com（主终结点：美国西部）
> - https://kailani1.one.microsoft.com（配对的故障转移区域：美国东部）
> - https://tm-kailani.one.microsoft.com（主要区域的发现 URL）

## <a name="summary-and-risk-limitation"></a>摘要和风险限制
本文档前面部分的列表包含 Azure 文件同步目前用来通信的 URL。 防火墙必须能够允许发往这些域的出站流量。 Microsoft 会不断更新此列表。

设置进行域限制的防火墙规则可以作为一项改进安全性的措施。 如果使用这些防火墙配置，则需注意，URL 会不断添加，甚至会随时更改。 请不时地回来查看本文。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)