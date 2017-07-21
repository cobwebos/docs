---
title: "Azure 文件存储简介 | Microsoft Docs"
description: "本文概述 Azure 文件存储，你可以通过该服务根据行业标准在 Microsoft 云中创建和使用网络文件共享。"
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
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 4393a9a71dcbdc709845eb4cd3078b4497b8b9a3
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# <a name="introduction-to-azure-file-storage"></a>Azure 文件存储简介
Azure 文件存储根据行业标准[服务器消息块 (SMB) 协议](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)和 [Samba/通用 Internet 文件系统 (CIFS)](https://technet.microsoft.com/library/cc939973.aspx) 在云中提供网络文件共享。 Azure 文件共享可以由客户端（例如本地部署的 Windows、macOS、Linux）或 Azure 虚拟机同时装载。 



## <a name="videos"></a>视频
| Azure 文件存储简介（27 分钟） | Azure 文件存储教程（5 分钟）  |
|-|-|
| [![Azure 文件存储简介视频屏幕截图 - 单击即可播放！](media/storage-file-storage/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Azure 文件存储教程屏幕截图 - 单击即可播放！](media/storage-file-storage/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>为何 Azure 文件存储很有用
Azure 文件存储允许你将基于 Windows Server、Linux 或 NAS 的文件服务器（托管在本地或云中）替换为无 OS 的云文件共享。 该功能有以下优点：

* 共享访问。 Azure 文件共享支持行业标准 SMB 协议，这意味着，你可以无缝地将本地文件共享替换为 Azure 文件共享，不需担心应用程序兼容性。 对于需要可共享性的应用程序来说，能够跨多个计算机、应用程序/实例共享文件系统是使用 Azure 文件存储的一项很大优势。 
* 完全托管。 不需管理硬件或 OS 即可创建 Azure 文件共享。 这意味着，你不需使用关键的安全升级程序来修补服务器 OS，也不需更换故障硬盘。
* 脚本和工具。 在管理 Azure 应用程序的过程中，可以使用 PowerShell cmdlet 和 Azure CLI 来创建、装载和管理文件存储共享。可以使用 Azure 门户和 Azure 存储资源管理器来创建和管理 Azure 文件共享。 
* 复原能力。 Azure 文件存储是由我们从头开始构建的，我们的目的是确保其始终可用。 将本地文件共享替换为 Azure 文件存储以后，你再也不需半夜醒来处理本地的断电或网络问题。 
* 熟悉的可编程性。 在 Azure 中运行的应用程序可以通过文件[系统 I/O API](https://msdn.microsoft.com/library/system.io.file.aspx) 访问共享中的数据。 因此，开发人员可以利用其现有代码和技术迁移现有应用程序。 除了系统 IO API，还可以使用 [Azure 存储客户端库](https://msdn.microsoft.com/library/azure/dn261237.aspx)或 [Azure 存储 REST API](/rest/api/storageservices/file-service-rest-api)。

Azure 文件共享可用于：

* 替换本地文件服务器：  
    可以使用 Azure 文件存储在传统的本地文件服务器或 NAS 设备上彻底替换文件共享。 常用操作系统（例如 Windows、macOS 和 Linux）可以在世界各地轻松地装载 Azure 文件共享。

* “直接迁移”应用程序：  
    可以使用 Azure 文件存储轻松地将应用程序“直接迁移”到云，以便使用本地文件共享在应用程序各部分之间共享数据。 为此，可以让每个 VM 连接到文件共享，然后读取和写入文件，就像针对本地文件共享进行操作一样。

* 简化云开发：  
    可以通过多种不同的方式使用 Azure 文件存储，简化新的云开发项目。
    * 共享应用程序设置：  
        分布式应用程序的常见模式是将配置文件置于某个中心位置，然后就可以从多个不同的 VM 访问该中心位置中的配置文件。 此类配置文件现在可以存储在 Azure 文件共享中，供所有应用程序实例读取。 这些设置也可以通过 REST 界面管理，这样全世界的用户都可以通过该界面访问配置文件。

    * 诊断共享：  
        也可使用 Azure 文件共享来保存诊断文件，例如日志、指标和故障转储。 允许通过 SMB 和 REST 界面使用这些文件，应用程序就可以生成或利用各种分析工具来处理和分析诊断数据。

    * 开发/测试/调试：  
        开发人员或管理员在云中的 VM 上工作时，通常需要一套工具或实用程序。 根据需要在每个虚拟机上安装和分发这些实用程序可能很费时间。 有了 Azure 文件存储，开发人员或管理员就可以将最喜爱的工具存储在文件共享中，然后即可轻松地从任何虚拟机连接到这些工具。
        
## <a name="how-does-it-work"></a>工作原理
管理 Azure 文件共享比管理本地文件共享容易得多。 下图说明了 Azure 文件存储管理构造：

![文件结构](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **存储帐户**：对 Azure 存储服务的所有访问都要通过存储帐户来完成。 有关存储帐户容量的详细信息，请参阅“Azure 存储可伸缩性和性能目标”。
* 共享：在 Azure 中，文件存储共享是 SMB 文件共享。 所有目录和文件都必须在父共享中创建。 一个帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到文件共享的 5TB 总容量限制为止。
* 目录：可选的目录层次结构。
* 文件：共享中的文件。 文件大小最大可以为 1 TB。
* URL 格式：可使用以下 URL 格式对文件寻址：  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## <a name="next-steps"></a>后续步骤
* [创建 Azure 文件共享](storage-file-how-to-create-file-share.md)
* [在 Windows 上进行连接和装载](storage-file-how-to-use-files-windows.md)
* [在 Linux 上进行连接和装载](storage-how-to-use-files-linux.md)
* [在 macOS 上进行连接和装载](storage-file-how-to-use-files-mac.md)
* [常见问题](storage-files-faq.md)
* [故障排除](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>概念性文章和视频
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)（Azure 文件存储：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统）

### <a name="tooling-support-for-azure-file-storage"></a>Azure 文件存储的工具支持
* [对 Azure 存储空间使用 Azure PowerShell](storage-powershell-guide-full.md)
* [如何对 Microsoft Azure 存储使用 AzCopy](storage-use-azcopy.md)
* [将 Azure CLI 用于 Azure 存储](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="blog-posts"></a>博客文章
* [Azure 文件存储现已正式发布](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)（Azure 文件存储内部）
* [Microsoft Azure 文件服务简介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)（将数据迁移到 Azure 文件）

### <a name="reference"></a>引用
* [用于 .NET 的存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [文件服务 REST API 参考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

