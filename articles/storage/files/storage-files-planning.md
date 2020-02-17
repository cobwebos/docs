---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9b71c4a5c0f245d9da97dc8f096d15c5386bf919
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368614"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署

[Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 由于 Azure 文件是完全托管的，因此在生产方案中对其进行部署比部署和管理文件服务器或 NAS 设备简单得多。 本文介绍在组织内部署 Azure 文件共享以供生产使用时应考虑的主题。

## <a name="management-concepts"></a>管理概念

 下图说明了 Azure 文件管理构造：

![文件结构](./media/storage-files-introduction/files-concepts.png)

* **存储帐户**：对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户容量的详细信息，请参阅[标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **共享**：文件存储共享是 Azure 中的 SMB 文件共享。 所有目录和文件都必须在父共享中创建。 一个帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到文件共享的总容量。 高级和标准文件共享的总容量为 100 TiB。

* **目录**：可选的目录层次结构。

* **文件**：共享中的文件。 文件大小最大可以为 1 TiB。

* **URL 格式**：对于使用文件 REST 协议向 Azure 文件共享提出的请求，可采用以下 URL 格式对文件进行寻址：

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>数据访问方法

Azure 文件提供两个内置的简便数据访问方法，用户可单独使用或结合使用这些方法来访问数据：

1. **直接云访问**：可使用行业标准服务器消息块 (SMB) 协议或通过文件 REST API，由 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和/或 [Linux](storage-how-to-use-files-linux.md) 装载任意 Azure 文件共享。 使用 SMB，可直接在 Azure 中的文件共享上读取和写入共享文件。 若要装载在 Azure VM 上，操作系统中的 SMB 客户端必须至少支持 SMB 2.1。 若要装载在本地（例如用户工作站），工作站支持的 SMB 客户端必须至少支持 SMB 3.0（已加密）。 除 SMB 以外，新应用程序或服务可通过文件 REST 直接访问文件共享，该文件 REST 为软件开发提供简单可缩放的应用程序编程接口。
2. **Azure 文件同步**：可使用 Azure 文件同步将共享复制到本地或 Azure 中的 Windows Server。 用户可通过 SMB 或 NFS 共享等 Windows Server 访问文件共享。 这适用于要在远离 Azure 数据中心的位置访问和修改数据的方案，例如分支机构方案。 可在多个 Windows Server 终结点（例如多个分支机构）之间复制数据。 最后，可将数据分层到 Azure 文件，以便所有数据仍可通过 Server 进行访问，但 Server 没有完整的数据副本。 相反，数据由用户打开时会被无缝召回。

下表说明了用户和应用程序如何访问 Azure 文件共享：

| | 直接云访问 | Azure 文件同步 |
|------------------------|------------|-----------------|
| 需使用哪些协议？ | Azure 文件支持 SMB 2.1、SMB 3.0 和文件 REST API。 | 通过 Windows Server 上支持的任意协议（SMB、NFS、FTPS 等）访问 Azure 文件共享 |  
| 在何处运行工作负荷？ | **在 Azure 中**：Azure 文件支持直接访问数据。 | **网络速度慢的本地文件共享**：Windows、Linux 和 macOS 客户端可以将本地 Windows 文件共享装载为 Azure 文件共享的快速缓存。 |
| 需要何种级别的 ACL？ | 共享和文件级别。 | 共享、文件和用户级别。 |

## <a name="data-security"></a>数据安全

Azure 文件提供可确保数据安全的几个内置选项：

* 支持以下两种在线协议加密：SMB 3.0 加密和通过 HTTPS 的文件 REST。 默认情况下： 
    * 支持 SMB 3.0 加密的客户端通过加密通道发送和接收数据。
    * 不支持通过加密的 SMB 3.0 的客户端可以在不加密的情况下通过 SMB 2.1 或 SMB 3.0 进行数据中心通信。 不允许 SMB 客户端通过无加密功能的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。
    * 客户端可以通过 HTTP 或 HTTPS 与文件 REST 通信。
* 静态加密（[Azure 存储服务加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)）：存储服务加密 (SSE) 对所有存储帐户启用。 静态数据使用完全托管的密钥进行加密。 静态加密不会增加存储成本，也不会降低性能。 
* 加密数据在传输中的可选要求：选择此项时，Azure 文件将拒绝通过未加密的通道访问数据。 具体而言，仅允许具有加密连接的 HTTPS 和 SMB 3.0。

    > [!Important]  
    > 要求安全传输数据将导致较早的 SMB 客户端无法与 SMB 3.0 通信，进而造成加密失败。 有关详细信息，请参阅[在 Windows 上装载](storage-how-to-use-files-windows.md)、[在 Linux 上装载](storage-how-to-use-files-linux.md)和[在 macOS 上装载](storage-how-to-use-files-mac.md)。

为了实现最大安全性，强烈建议始终启用这两个静态加密功能，并在使用新式客户端访问数据时启用数据传输加密。 例如，如需在仅支持 SMB 2.1 的 Windows Server 2008 R2 VM 上装载共享，则需要允许存储帐户接受未加密的流量，因为 SMB 2.1 不支持加密。

如果使用 Azure 文件同步访问 Azure 文件共享，我们将始终使用 HTTPS 和加密的 SMB 3.0 将数据同步到 Windows Server，而不考虑是否需要对静态数据加密。

## <a name="file-share-performance-tiers"></a>文件共享性能层

Azure 文件提供两个性能层：标准和高级。

### <a name="standard-file-shares"></a>标准文件共享

标准文件共享支持硬盘驱动器（Hdd）。 标准文件共享为 IO 工作负荷提供可靠的性能，这些工作负荷对性能变化（如一般用途文件共享和开发/测试环境）不敏感。 标准文件共享只能在即用即付计费模型下使用。

> [!IMPORTANT]
> 如果要使用大于 5 TiB 的文件共享，请参阅板载[到较大的文件共享（标准层）](#onboard-to-larger-file-shares-standard-tier)部分，了解加入的步骤，以及区域可用性和限制。

### <a name="premium-file-shares"></a>高级文件共享

高级文件共享由固态硬盘（Ssd）支持。 对于 IO 密集型工作负荷，高级文件共享为大多数 IO 操作提供持续的高性能和低延迟。 这使得它们适用于各种工作负荷，如数据库、网站托管和开发环境。 高级文件共享只能在预配的计费模型下使用。 高级文件共享使用与标准文件共享分离的部署模型。

Azure 备份适用于高级文件共享，Azure Kubernetes 服务支持版本1.13 及更高版本中的高级文件共享。

如果你想要了解如何创建高级文件共享，请参阅主题的文章：[如何创建 Azure 高级文件存储帐户](storage-how-to-create-premium-fileshare.md)。

目前，不能在标准文件共享和高级文件共享之间直接转换。 如果要切换到任一层，必须在该层中创建新的文件共享，并手动将数据从原始共享复制到所创建的新共享。 可以使用支持的任何 Azure 文件复制工具（例如 Robocopy 或 AzCopy）来执行此操作。

> [!IMPORTANT]
> 在大多数区域中，LRS 提供高级文件共享，这些区域提供存储帐户，并在较小的区域部分中使用 ZRS。 若要确定高级文件共享当前是否在你的区域中可用，请参阅 Azure 的 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)" 页。 有关支持 ZRS 的区域的信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md)。
>
> 若要帮助我们确定新的区域和高级层功能的优先级，请填写此[调查](https://aka.ms/pfsfeedback)。

#### <a name="provisioned-shares"></a>预配的共享

高级文件共享是基于固定的 GiB/IOPS/吞吐量比率预配的。 对于预配的每个 GiB，将向该共享分配 1 IOPS 和 0.1 MiB/秒的吞吐量，最多可达每个共享的最大限制。 允许的最小预配为 100 GiB 以及最小的 IOPS/吞吐量。

最大限度地提供服务时，对于预配的存储，所有共享都可以突增到每 GiB 3 IOPS，持续 60 分钟或更长时间，具体取决于共享大小。 新共享将根据预配的容量以完全突增额度开始。

必须以 1 GiB 为增量预配共享。 最小大小为 100 GiB，下一大小为 101 GiB，依此类推。

> [!TIP]
> 基线 IOPS = 1 * 预配的 GiB。 （最大可达 100000 IOPS）。
>
> 突发限制 = 3 * 基准 IOPS。 （最大可达 100000 IOPS）。
>
> 出口速率 = 60 MiB/s + 0.06 * 预配 GiB
>
> 入口速率 = 40 MiB/s + 0.04 * 预配 GiB

预配的共享大小由共享配额指定。 共享配额可随时增加，但只能在自上次增加之后24小时后减少。 等待24小时后，如果没有增加配额，你可以根据需要将共享配额减少多次，直到再次增加。 在大小改变后的几分钟内，IOPS/吞吐量规模更改将生效。

可以在使用的 GiB 下减小预配共享的大小。 如果执行此操作，则不会丢失数据，但仍会对所用的大小进行计费，并获得预配的共享的性能（基线 IOPS、吞吐量和突发 IOPS），而不是所使用的大小。

下表演示了预配共享大小的这些公式的几个示例：

|容量（GiB） | 基线 IOPS | 突发 IOPS | 出口（MiB/秒） | 入口（MiB/秒） |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | 最高1500   | 90   | 60   |
|1,024       | 1,024   | 最高3072   | 122   | 81   |
|5,120       | 5,120   | 最高15360  | 368   | 245   |
|10,240      | 10,240  | 最高30720  | 675 | 450   |
|33,792      | 33,792  | 最高100000 | 2,088 | 1,392   |
|51,200      | 51,200  | 最高100000 | 3,132 | 2,088   |
|102,400     | 100,000 | 最高100000 | 6,204 | 4,136   |

> [!NOTE]
> 文件共享性能受到计算机网络限制、可用网络带宽、IO 大小、并行性的限制，还有许多其他因素。 例如，基于具有8个 KiB 读取/写入 IO 大小的内部测试，通过 SMB 连接到高级文件共享的单个 Windows 虚拟机（*标准 F16s_v2*）可实现20K 读取 Iops 和15K 写入 iops。 对于 512 MiB 读取/写入 IO 大小，同一 VM 可以实现 1.1 GiB/s 出口和 370 MiB/秒的入口吞吐量。 若要实现最大性能规模，请将负载分散到多个 Vm。 请参阅[故障排除指南](storage-troubleshooting-files-performance.md)，了解一些常见问题和解决方法。

#### <a name="bursting"></a>冲

高级文件共享可能会将其 IOPS 突发为三倍。 突发是自动进行的，并基于信用系统运行。 突发会尽力工作，而突发限制并不保证，文件共享可能会突然*达到*限制。

如果文件共享的流量低于基线 IOPS，信用额度会在突发桶中累积。 例如，100 GiB 共享有100的基线 IOPS。 如果共享上的实际流量为特定1秒间隔的 40 IOPS，则会将60未使用的 IOPS 贷记到突发桶。 以后在操作超过基线 IOPs 时，将使用这些信用额度。

> [!TIP]
> 突发流量桶的大小 = 基线 IOPS * 2 * 3600。

只要共享超过基线 IOPS，并在突发桶中包含信用额度，就会突然增长。 只要剩余信用额度，共享就可以继续突发，不过，小于 50 TiB 的共享只会保持最多一小时的突发限制。 超过 50 TiB 的共享在技术上可以超过此小时的限制，最长可达两个小时，但这取决于所应计的突发点数。 超出基线 IOPS 的每个 IO 消耗一个信用额度，一旦使用了所有信用额度，该共享就会返回到基线 IOPS。

共享信用包含三种状态：

- 如果文件共享使用的不是基线 IOPS，则为。
- 正在拒绝，当文件共享为突发。
- 当未使用任何信用额度或基线 IOPS 时剩余的常量。

新文件共享在其突发桶中以全部信用额度开头。 如果由于服务器限制而导致共享 IOPS 低于基准 IOPS，则不会对爆发信用进行应计。

## <a name="file-share-redundancy"></a>文件共享冗余

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

如果选择使用读取访问异地冗余存储（GRS），则应知道 Azure 文件目前不支持任何区域的读取访问异地冗余存储（GRS）。 GRS 存储帐户中的文件共享的工作方式与其在 GRS 帐户中的工作方式相同，并按 GRS 价格收费。

> [!Warning]  
> 如果在 GRS 存储帐户中使用 Azure 文件共享作为云终结点，则不应启动存储帐户故障转移。 执行此操作将导致同步停止工作，并且还可能导致新分层的文件出现意外数据丢失。 对于 Azure 区域丢失，Microsoft 会以与 Azure 文件同步兼容的方式触发存储帐户故障转移。

Azure 文件 premium 共享支持 LRS 和 ZRS，ZRS 目前在一小部分区域中提供。

## <a name="onboard-to-larger-file-shares-standard-tier"></a>集成到较大的文件共享（标准层）

本部分仅适用于标准文件共享。 所有高级文件共享都可用 100 TiB 容量。

### <a name="restrictions"></a>限制

- 对于启用了大文件共享的任何存储帐户，将无法 LRS/ZRS 转换为 GRS/GZRS 帐户转换。

### <a name="regional-availability"></a>区域可用性

100 TiB 容量限制的标准文件共享在所有 Azure 区域中全局可用-

- LRS除南非北部和南非西部之外的所有区域。
- ZRS除日本东部、北欧、南非北部以外的所有区域。
- GRS/GZRS：不受支持。

### <a name="enable-and-create-larger-file-shares"></a>启用和创建更大的文件共享

若要开始使用较大的文件共享，请参阅文章[如何启用和创建大型文件共享](storage-files-how-to-create-large-file-share.md)。

## <a name="data-growth-pattern"></a>数据增长模式

目前，Azure 文件共享的最大大小为 100 TiB。 鉴于此当前限制，必须考虑部署 Azure 文件共享时的预期数据增长。

可使用 Azure 文件同步将多个 Azure 文件共享同步到单个 Windows 文件服务器。这可确保本地的较旧、大型文件共享能够导入到 Azure 文件同步。有关详细信息，请参阅[规划 Azure 文件同步部署](storage-files-planning.md)。

## <a name="data-transfer-method"></a>数据传输方法

可通过多种简单的选项将数据从现有文件共享（例如本地文件共享）批量传输到 Azure 文件。 几种常用选项包括（非详尽列表）：

* **[Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** ：在 Azure 文件共享（“云终结点”）和 Windows 目录命名空间（“服务器终结点”）之间首次同步期间，Azure 文件同步将把现有文件共享中的所有数据复制到 Azure 文件。
* **[Azure 导入/导出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：使用 Azure 导入/导出服务，可将硬盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure 文件共享。 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ：Robocopy 是 Windows 和 Windows Server 自带的一款知名复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：AzCopy 是一个命令行实用程序，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
