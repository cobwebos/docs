---
title: "有关 Azure 文件的常见问题解答 | Microsoft Docs"
description: "查看有关 Azure 文件的常见问题解答。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eb0b4d7cde568ab1809daa8025120828e75f5d76
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="frequently-asked-questions-about-azure-files"></a>有关 Azure 文件的常见问题解答

## <a name="general"></a>常规
* **问：什么是 Azure 文件？**  
   
    Azure 文件是 Azure 分布式文件系统。 它提供 SMB 协议接口，可允许用户在支持的 Azure 虚拟机或本地计算机上将存储装载为本机共享。

* **问：Azure 文件为什么很有用？**  
   
    Azure 文件支持跨多个 VM 和平台访问共享数据。 请参阅[为何 Azure 文件很有用](storage-files-introduction.md#why-azure-files-is-useful)。

* **问：何时应使用 Azure 文件、Azure Blob 与 Azure 磁盘？**  
   
    Microsoft Azure 提供多种方式，以便用户可以在云中存储和访问数据。  
   
    Azure 文件 - 提供 SMB 接口、客户端库，以及方便从任意位置轻松访问已存储文件的 REST 接口。  
   
    Azure Blob - 提供客户端库，以及方便在块 blob 中批量存储和访问非结构化数据的 REST 接口。  
   
    Azure 数据磁盘 - 提供客户端库，以及方便通过附加的虚拟硬盘一致地存储和访问数据的 REST 接口。  
   
    详细了解如何[确定何时使用 Azure Blob、Azure 文件存储或 Azure 数据磁盘](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* **问：如何开始使用 Azure 文件？**  
   
    可以从创建 Azure 文件共享入手。 可以使用 Azure 门户、Azure 存储 PowerShell cmdlet、Azure 存储客户端库或 Azure 存储 REST API 来创建 Azure 文件共享。在本教程中，将了解：

    * [如何使用门户创建 Azure 文件共享](storage-how-to-use-files-portal.md)
    * [如何使用 Powershell 创建 Azure 文件共享](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [如何使用 CLI 创建 Azure 文件共享](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **问：Azure 文件支持哪些类型的复制？**  
   
    Azure 文件现在仅支持 LRS 或 GRS。 我们计划支持 RA-GRS，但尚没有共享时间表。
    
## <a name="scale-targetslimits"></a>缩放目标/限制

* **问：多少个客户端可以同时访问同一文件？**

    单个文件有 2000 个打开句柄配额。 一旦有 2000 个打开句柄，就会收到错误，指示已达到配额。

## <a name="security-authentication-and-access-control"></a>安全性、身份验证和访问控制

* **问：访问 Azure 文件中的文件有哪些不同方式？**
    
    可以使用 SMB 3.0 协议将文件共享装载到本地计算机上，也可以使用[存储资源管理器](http://storageexplorer.com/)等工具访问文件共享中的文件。 在应用程序中，可以使用存储客户端库、REST API 或 Powershell 访问 Azure 文件共享中的文件。

* **问：如何使用 Web 浏览器提供对特定文件的访问权限？**
    
    使用 SAS，可以生成在指定时间间隔内有效的特定权限令牌。 例如，可以生成一个令牌，该令牌只能在特定时段内以只读方式访问特定文件。 只要拥有有效的此 URL，就可以直接通过任意 Web 浏览器访问文件。 SAS 密钥可以轻松地从 UI（例如存储资源管理器）生成。

* **问：能否对共享中的文件夹指定只读或只写权限？**
    
    如果通过 SMB 装载文件共享，不具有此级别的权限控制。 但是，可以通过 REST API 或客户端库创建共享访问签名 (SAS) 来实现此控制。  

* **问：如何针对 Azure 文件启用服务器端加密？**

    Azure 文件的[服务器端加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)通常在所有区域和公有云及国家云中可用。 可以使用 [Azure 门户](https://portal.azure.com/)、[Microsoft Azure 存储资源提供程序 API](/rest/api/storagerp/storageaccounts)、[Azure Powershell](https://msdn.microsoft.com/library/azure/mt607151.aspx) 或 [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 为 Azure 文件启用 SSE。
    
    在 Azure 文件上启用 SSE 后，写入到该存储帐户中文件存储的任何新数据都会自动加密。 此功能可用于写入到现有或新存储帐户中的现有或新共享的所有新数据。 启用此功能不需额外付费。 详细了解[如何为 Azure 文件启用 SSE](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **问：Azure 文件是否支持基于 Active Directory 的身份验证？**
   
    我们目前不支持基于 AD 的身份验证或 ACL，但会将其列入我们的功能请求列表中。 目前，Azure 存储帐户密钥用于为文件共享提供身份验证。 我们确实提供通过 REST API 或客户端库使用共享访问签名 (SAS) 的解决方法。 使用 SAS，可以生成在指定时间间隔内有效的特定权限令牌。 例如，可以生成在 10 分钟后到期的令牌，其中包含对给定文件的只读访问权限。 只要拥有有效的此令牌，就可以在 10 分钟内拥有对给定文件的只读访问权限。
   
    仅通过 REST API 或客户端库支持 SAS。 通过 SMB 协议装载文件共享时，不能使用 SAS 委派对其内容的访问权限。 
    
* **问：什么是 Azure 文件支持的数据符合性策略？**

   Azure 文件所依据的存储体系结构与 Azure 存储中的其他存储服务相同，并且实施的数据符合性策略也相同。 有关 Azure 存储数据符合性的详细信息，可以下载并参阅 [Microsoft Azure 数据保护文档](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)。

## <a name="on-premises-access"></a>本地访问

* **问：必须使用 Azure ExpressRoute 才能从本地 VM 连接到 Azure 文件吗？**
   
    不会。 如果没有 ExpressRoute，仍可从本地访问文件共享，只需将端口 445（TCP 出站）打开供 Internet 访问即可。 不过，如果愿意，可以将 ExpressRoute 用于 Azure 文件。

* **问：如何才能在本地计算机上装载 Azure 文件共享？** 
    
    可以通过 SMB 协议装载文件共享，只要端口 445（TCP 出站）处于打开状态，且客户端支持 SMB 3.0 协议（例如，使用的是 Windows 10 或 Windows Server 2012）。 请通过本地 ISP 提供商来取消阻止端口。 在过渡期间，可以使用[存储资源管理器](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents)查看文件。


## <a name="billing-and-pricing"></a>计费和定价
* **问：Azure VM 与文件共享之间的网络流量是否作为外部带宽计入订阅费用？**
   
    如果文件共享和 VM 位于同一 Azure 区域，它们之间的流量是免费的。 如果文件共享和 VM 位于不同的区域，它们之间的流量作为外部带宽收费。

## <a name="backup"></a>备份

* **问：如何备份我的 Azure 文件共享？**
    
    可以使用 AzCopy、RoboCopy 或能够备份已装载文件共享的第三方备份工具。 在不久的将来，我们预计推出方便用户生成文件共享快照的功能；用户将可以使用此功能来备份 Azure 文件共享。

## <a name="performance"></a>性能

* **问：Azure 文件存在哪些缩放限制？**
    若要了解 Azure 文件的可伸缩性和性能目标，请参阅 [Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files)。

* **问：尝试将文件解压缩到 Azure 文件中时性能降低。我该怎样做？**
    
    若要将大量文件传输到 Azure 文件，建议使用 AzCopy（Windows 版、Linux/Unix 预览版）或 Azure Powershell，因为这些工具已针对网络传输进行优化。

* **问：为了修复 Azure 文件存在的性能降低问题，发布了哪些修补程序？**
    
    Windows 团队最近发布了一个修补程序，旨在修复客户通过 Windows 8.1 或 Windows Server 2012 R2 访问 Azure 文件时遇到的性能降低问题。 有关详细信息，请查看相关的知识库文章：[Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025)（从 Windows 8.1 或 Server 2012 R2 访问 Azure 文件时性能降低）。

## <a name="features-and-interoperability-with-other-services"></a>功能以及与其他服务的互操作性
* **问：故障转移群集的“文件共享见证”是 Azure 文件的用例之一吗？**
   
    暂不支持。

* **问：REST API 中是否有重命名操作？**
   
    现在不行。

* **问：能否使用嵌套共享？也就是说，能否在共享下再使用共享？**
    
    否。 文件共享是可以装载的虚拟驱动程序，因此不支持嵌套共享。

* **问：将 Azure 文件与 IBM MQ 配合使用**
    
    IBM 已发布相关文档，指导 IBM MQ 客户通过其服务配置 Azure 文件。 有关详细信息，请查阅 [如何通过 Microsoft Azure 文件服务设置 IBM MQ 多实例队列管理器](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)。


## <a name="troubleshooting"></a>故障排除
* **问：如何排查 Azure 文件错误？**
    
    可以参考 [Azure 文件故障排除文章](storage-troubleshoot-windows-file-connection-problems.md)了解有关端到端故障排除指南。 


## <a name="see-also"></a>另请参阅
请参阅以下链接，获取有关 Azure 文件的更多信息。

### <a name="conceptual-articles-and-videos"></a>概念性文章和视频
* [Azure 文件：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [如何通过 Linux 使用 Azure 文件](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>文件存储的工具支持
* [如何对 Microsoft Azure 存储使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [将 Azure CLI 用于 Azure 存储](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>博客文章
* [Azure 文件现已正式发布](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 文件内部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 文件服务简介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [将数据迁移到 Azure 文件](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>引用
* [.NET 存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [文件服务 REST API 参考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

