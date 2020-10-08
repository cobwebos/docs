---
title: Azure 文件简介 | Microsoft Docs
description: 本文概述 Azure 文件服务，使用该服务可以通过行业标准 SMB 协议在云中创建和使用网络文件共享。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2b5fa5211ad8d4de01f2b63e8267e297e13c4485
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91570019"
---
# <a name="what-is-azure-files"></a>什么是 Azure 文件？
Azure 文件存储会在云中提供完全托管的文件共享，这些共享项可通过行业标准的[服务器消息块 (SMB) 协议](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)或[网络文件系统 (NFS) 协议](https://en.wikipedia.org/wiki/Network_File_System)进行访问。 Azure 文件共享可通过云部署或者本地部署并行装载。 可从 Windows、Linux 和 macOS 客户端访问Azure 文件存储 SMB 文件共享。 可从 Linux 或 macOS 客户端访问 Azure 文件存储 NFS 文件共享。 此外，可以使用 Azure 文件同步将 Azure 文件存储 SMB 文件共享缓存在 Windows Server 上，以供在使用数据的位置附近快速访问。

## <a name="videos"></a>视频
| Azure 文件同步简介 | Azure 文件存储与同步 (Ignite 2019)  |
|-|-|
| [![Azure 文件同步简介视频截屏 - 单击播放！](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Azure 文件与同步演示截屏 - 单击播放！](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

下面是有关 Azure 文件存储的常见用例的一些视频：
* [Replace your file server with a serverless Azure File Share](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)（将你的文件服务器替换为无服务器的 Azure 文件共享）
* [Getting started with FSLogix profile containers on Azure Files in Windows Virtual Desktop leveraging AD authentication](https://www.youtube.com/embed/9S5A1IJqfOQ)（Windows 虚拟桌面中 Azure 文件存储上的 FSLogix 配置文件容器入门（利用 AD 身份验证））

## <a name="why-azure-files-is-useful"></a>为何 Azure 文件很有用
Azure 文件共享可用于：

* **取代或补充本地文件服务器**：  
    可以使用 Azure 文件来完全取代或补充传统的本地文件服务器或 NAS 设备。 流行的操作系统（例如 Windows、macOS 和 Linux）可在世界各地直接装载 Azure 文件共享。 此外，还可以使用 Azure 文件同步将 Azure 文件存储 SMB 文件共享复制到本地或云中的 Windows Server，以便在使用数据的位置对数据进行高性能的分布式缓存。 使用最新版本的 [Azure 文件存储 AD 身份验证](storage-files-active-directory-overview.md)，Azure 文件存储 SMB 文件共享可继续使用本地托管的 AD 进行访问控制。 

* **“直接迁移”应用程序**：  
    借助 Azure 文件可以轻松地将预期使用文件共享存储文件应用程序或用户数据的应用程序“直接迁移”到云中。 Azure 文件既支持“经典”直接迁移方案（应用程序及其数据将移到 Azure 中），也支持“混合”直接迁移方案（应用程序数据将移到 Azure 文件中，应用程序继续在本地运行）。 

* **简化云开发**：  
    还可以通过众多方式使用 Azure 文件来简化新的云开发项目。 例如：
    * **共享应用程序设置**：  
        分布式应用程序的常见模式是将配置文件置于某个中心位置，然后可以从许多应用程序实例访问这些文件。 应用程序实例可以通过文件 REST API 加载其配置，人类可以根据需要通过本地装载 SMB 共享来访问这些配置。

    * **诊断共享**：  
        Azure 文件共享是云应用程序写入其日志、指标和故障转储的方便位置。 应用程序实例可以通过文件 REST API 写入日志，开发人员可以通过在其本地计算机上装载文件共享来访问这些日志。 这就带来了极大的灵活性，因为开发人员可以利用云开发，同时又不需要放弃他们所熟悉和喜爱的任何现有工具。

    * **开发/测试/调试**：  
        开发人员或管理员在云中的 VM 上工作时，通常需要一套工具或实用程序。 将此类实用程序和工具复制到每个 VM 可能非常耗时。 通过在 VM 上本地装载 Azure 文件共享，开发人员和管理员可以快速访问其工具和实用程序，而无需进行复制。
* **容器化**：  
    可以将 Azure 文件共享用作有状态容器的永久性卷。 容器提供了“一次构建，随处运行”功能，使开发人员能够加速创新。 对于在每次启动时都访问原始数据的容器，需要使用共享文件系统，以允许这些容器无论在哪个实例上运行都可以访问文件系统。

## <a name="key-benefits"></a>主要优点
* **共享访问**。 Azure 文件共享支持行业标准 SMB 协议和 NFS 协议，这意味着，你可以无缝地将本地文件共享替换为 Azure 文件共享，而无需担心应用程序兼容性。 对于需要可共享性的应用程序来说，能够跨多个计算机、应用程序/实例共享文件系统是使用 Azure 文件的一项明显优势。 
* **完全托管**。 不需管理硬件或 OS 即可创建 Azure 文件共享。 这意味着，你不需使用关键的安全升级程序来修补服务器 OS，也不需更换故障硬盘。
* **脚本和工具**。 在管理 Azure 应用程序的过程中，可以使用 PowerShell cmdlet 和 Azure CLI 来创建、装载和管理 Azure 文件共享。可以使用 Azure 门户和 Azure 存储资源管理器来创建和管理 Azure 文件共享。 
* **复原能力**。 Azure 文件是从头开始构建的，我们的目的是确保其始终可用。 将本地文件共享取代为 Azure 文件之后，再也不需要半夜起来处理当地断电或网络问题。 
* **熟悉的可编程性**。 在 Azure 中运行的应用程序可以通过文件[系统 I/O API](https://msdn.microsoft.com/library/system.io.file.aspx) 访问共享中的数据。 因此，开发人员可以利用其现有代码和技术迁移现有应用程序。 除了系统 IO API，还可以使用 [Azure 存储客户端库](https://msdn.microsoft.com/library/azure/dn261237.aspx)或 [Azure 存储 REST API](/rest/api/storageservices/file-service-rest-api)。

## <a name="next-steps"></a>后续步骤
* [了解可用的文件共享协议](storage-files-compare-protocols.md)
* [创建 Azure 文件共享](storage-how-to-create-file-share.md)
* [在 Windows 上连接和装载 SMB 共享](storage-how-to-use-files-windows.md)
* [在 Linux 上连接和装载 SMB 共享](storage-how-to-use-files-linux.md)
* [在 macOS 上连接和装载 SMB 共享](storage-how-to-use-files-mac.md)
* [如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)
