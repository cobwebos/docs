---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: eece1520a4b7e3bf37e1d209c58b5019921fdb98
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884385"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署

[Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 由于 Azure 文件是完全托管的，因此在生产方案中对其进行部署比部署和管理文件服务器或 NAS 设备简单得多。 本文介绍在组织内部署 Azure 文件共享以供生产使用时应考虑的主题。

## <a name="management-concepts"></a>管理概念

 下图说明了 Azure 文件管理构造：

![文件结构](./media/storage-files-introduction/files-concepts.png)

* **存储帐户**：对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户容量的详细信息，请参阅[可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **共享**：文件存储共享是 Azure 中的 SMB 文件共享。 所有目录和文件都必须在父共享中创建。 一个帐户可以包含无限数量的共享, 一个共享可以存储无限数量的文件, 直到达到文件共享的总容量。 对于标准文件共享, 总容量最多为 5 TiB (GA) 或 100 TiB (预览版), 适用于高级文件共享, 总容量为 100 TiB。

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
    * 不支持带加密功能的 SMB 3.0 的客户端可通过无加密功能的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。 不允许 SMB 客户端通过无加密功能的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。
    * 客户端可以通过 HTTP 或 HTTPS 与文件 REST 通信。
* 静态加密（[Azure 存储服务加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)）：存储服务加密 (SSE) 对所有存储帐户启用。 静态数据使用完全托管的密钥进行加密。 静态加密不会增加存储成本，也不会降低性能。 
* 加密数据在传输中的可选要求: 选择此项时, Azure 文件将拒绝通过未加密的通道访问数据。 具体而言，仅允许具有加密连接的 HTTPS 和 SMB 3.0。

    > [!Important]  
    > 要求安全传输数据将导致较早的 SMB 客户端无法与 SMB 3.0 通信，进而造成加密失败。 有关详细信息，请参阅[在 Windows 上装载](storage-how-to-use-files-windows.md)、[在 Linux 上装载](storage-how-to-use-files-linux.md)和[在 macOS 上装载](storage-how-to-use-files-mac.md)。

为了实现最大安全性，强烈建议始终启用这两个静态加密功能，并在使用新式客户端访问数据时启用数据传输加密。 例如，如需在仅支持 SMB 2.1 的 Windows Server 2008 R2 VM 上装载共享，则需要允许存储帐户接受未加密的流量，因为 SMB 2.1 不支持加密。

如果使用 Azure 文件同步访问 Azure 文件共享，我们将始终使用 HTTPS 和加密的 SMB 3.0 将数据同步到 Windows Server，而不考虑是否需要对静态数据加密。

## <a name="file-share-performance-tiers"></a>文件共享性能层

Azure 文件提供两个性能层: 标准和高级。

### <a name="standard-file-shares"></a>标准文件共享

标准文件共享支持硬盘驱动器 (Hdd)。 标准文件共享为 IO 工作负荷提供可靠的性能, 这些工作负荷对性能变化 (如一般用途文件共享和开发/测试环境) 不敏感。 标准文件共享只能在即用即付计费模型下使用。

标准文件共享最多可提供5个 TiB。 尽管较大的文件共享 (这是大于 5 TiB 的任何共享, 最多可达 100 TiB), 但目前作为预览产品/服务。

> [!IMPORTANT]
> 有关加入的步骤以及预览范围和限制的详细步骤, 请参阅[板载到大型文件共享 (标准层)](#onboard-to-larger-file-shares-standard-tier)部分。

### <a name="premium-file-shares"></a>高级文件共享

高级文件共享由固态硬盘 (Ssd) 支持。 对于 IO 密集型工作负荷, 高级文件共享为大多数 IO 操作提供持续的高性能和低延迟。 这使得它们适用于各种工作负荷, 如数据库、网站托管和开发环境。 高级文件共享只能在预配的计费模型下使用。 高级文件共享使用与标准文件共享分离的部署模型。

Azure 备份适用于高级文件共享, Azure Kubernetes 服务支持版本1.13 及更高版本中的高级文件共享。

如果你想要了解如何创建高级文件共享, 请参阅主题的文章:[如何创建 Azure 高级文件存储帐户](storage-how-to-create-premium-fileshare.md)。

目前, 不能在标准文件共享和高级文件共享之间直接转换。 如果要切换到任一层, 必须在该层中创建新的文件共享, 并手动将数据从原始共享复制到所创建的新共享。 可以使用支持的任何 Azure 文件复制工具 (例如 Robocopy 或 AzCopy) 来执行此操作。

> [!IMPORTANT]
> 高级文件共享仅适用于 LRS, 可用于提供存储帐户的大多数区域。 若要确定高级文件共享当前是否在你的区域中可用, 请参阅 Azure 的 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)" 页。

#### <a name="provisioned-shares"></a>预配的共享

高级文件共享是基于固定的 GiB/IOPS/吞吐量比率预配的。 对于预配的每个 GiB，将向该共享分配 1 IOPS 和 0.1 MiB/秒的吞吐量，最多可达每个共享的最大限制。 允许的最小预配为 100 GiB 以及最小的 IOPS/吞吐量。

最大限度地提供服务时，对于预配的存储，所有共享都可以突增到每 GiB 3 IOPS，持续 60 分钟或更长时间，具体取决于共享大小。 新共享将根据预配的容量以完全突增额度开始。

必须以 1 GiB 为增量预配共享。 最小大小为 100 GiB, 下一大小为 101 GiB, 依此类推。

> [!TIP]
> 基线 IOPS = 1 * 预配的 GiB。 (最大可达 100000 IOPS)。
>
> 突发限制 = 3 * 基准 IOPS。 (最大可达 100000 IOPS)。
>
> 出口速率 = 60 MiB/s + 0.06 * 预配 GiB
>
> 入口速率 = 40 MiB/s + 0.04 * 预配 GiB

共享大小可以在任何时间增加, 但只能在自上次增加后的24小时后减少。 等待24小时后, 如果未增加大小, 你可以根据需要将共享大小减少多次, 直到再次增加。 在大小改变后的几分钟内, IOPS/吞吐量规模更改将生效。

可以在使用的 GiB 下减小预配共享的大小。 如果执行此操作, 则不会丢失数据, 但仍会对所用的大小进行计费, 并获得预配的共享的性能 (基线 IOPS、吞吐量和突发 IOPS), 而不是所使用的大小。

下表演示了预配共享大小的这些公式的几个示例:

|容量 (GiB) | 基线 IOPS | 突发 IOPS | 出口 (MiB/秒) | 入口 (MiB/秒) |
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
> 文件共享性能受到计算机网络限制、可用网络带宽、IO 大小、并行性的限制, 还有许多其他因素。 若要实现最大性能规模, 请将负载分散到多个 Vm。 请参阅[故障排除指南](storage-troubleshooting-files-performance.md), 了解一些常见问题和解决方法。

#### <a name="bursting"></a>冲

高级文件共享可能会将其 IOPS 突发为三倍。 突发是自动进行的, 并基于信用系统运行。 突发会尽力工作, 而突发限制并不保证, 文件共享可能会突然*达到*限制。

如果文件共享的流量低于基线 IOPS, 信用额度会在突发桶中累积。 例如, 100 GiB 共享有100的基线 IOPS。 如果共享上的实际流量为特定1秒间隔的 40 IOPS, 则会将60未使用的 IOPS 贷记到突发桶。 以后在操作超过基线 IOPs 时, 将使用这些信用额度。

> [!TIP]
> 突发流量桶的大小 = 基线 IOPS * 2 * 3600。

只要共享超过基线 IOPS, 并在突发桶中包含信用额度, 就会突然增长。 只要剩余信用额度, 共享就可以继续突发, 不过, 小于 50 TiB 的共享只会保持最多一小时的突发限制。 超过 50 TiB 的共享在技术上可以超过此小时的限制, 最长可达两个小时, 但这取决于所应计的突发点数。 超出基线 IOPS 的每个 IO 消耗一个信用额度, 一旦使用了所有信用额度, 该共享就会返回到基线 IOPS。

共享信用包含三种状态:

- 如果文件共享使用的不是基线 IOPS, 则为。
- 正在拒绝, 当文件共享为突发。
- 当未使用任何信用额度或基线 IOPS 时剩余的常量。

新文件共享在其突发桶中以全部信用额度开头。 如果由于服务器限制而导致共享 IOPS 低于基准 IOPS, 则不会对爆发信用进行应计。

## <a name="file-share-redundancy"></a>文件共享冗余

Azure 文件标准共享支持三个数据冗余选项: 本地冗余存储 (LRS)、区域冗余存储 (ZRS) 和异地冗余存储 (GRS)。

Azure 文件 premium 共享仅支持本地冗余存储 (LRS)。

以下部分介绍了不同的冗余选项之间的差异：

### <a name="locally-redundant-storage"></a>本地冗余存储

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>区域冗余存储

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>异地冗余存储

> [!Warning]  
> 如果在 GRS 存储帐户中使用 Azure 文件共享作为云终结点，则不应启动存储帐户故障转移。 执行此操作将导致同步停止工作，并且还可能导致新分层的文件出现意外数据丢失。 对于 Azure 区域丢失，Microsoft 会以与 Azure 文件同步兼容的方式触发存储帐户故障转移。

异地冗余存储 (GRS) 通过将数据复制到距主要区域数百英里以外的次要区域，用于在给定的一年内至少为对象提供 99.99999999999999%（16 个 9）的持久性。 如果存储帐户启用了 GRS，则即使遇到区域完全停电或导致主区域不可恢复的灾难，数据也能持久保存。

如果你选择读取访问权限异地冗余存储 (RA-GRS)，则应当知道 Azure 文件目前在任何区域都不支持读取访问权限异地冗余存储 (RA-GRS)。 GRS 存储帐户中的文件共享的工作方式与其在 GRS 帐户中的工作方式相同, 并按 GRS 价格收费。

GRS 将数据复制到次要区域中的另一个数据中心，但仅当 Microsoft 发起了从主要区域到次要区域的故障转移时，才可读取这些数据。

对于已启用 GRS 的存储帐户，首先会使用本地冗余存储 (LRS) 复制所有数据。 首先将更新提交到主要位置，并使用 LRS 复制更新。 然后，使用 GRS 以异步方式将更新复制到次要区域。 将数据写入次要位置后，还会使用 LRS 在该位置复制数据。

主要和次要区域在一个存储缩放单元内管理跨单独的容错域和升级域管理副本。 存储缩放单元是数据中心内的基本复制单元。 此级别的复制由 LRS 提供；有关详细信息，请参阅[本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](../common/storage-redundancy-lrs.md)。

确定要使用哪个复制选项时，请记住以下几点：

* 区域冗余存储 (ZRS) 通过同步复制提供高可用性, 对于某些方案, 可能是比 GRS 更好的选择。 有关 ZRS 的详细信息，请参阅 [ZRS](../common/storage-redundancy-zrs.md)。
* 对于异步复制，从数据写入到主要区域到数据复制到次要区域，这之间存在延迟。 发生区域性灾难时，如果无法从主要区域中恢复数据，则尚未复制到次要区域的更改可能会丢失。
* 使用 GRS 时，副本不可用于读取或写入访问，除非 Microsoft 启动到次要区域的故障转移。 如果发生故障转移，则在故障转移完成后，你将具有对该数据的读取和写入访问权限。 有关详细信息，请参阅[灾难恢复指南](../common/storage-disaster-recovery-guidance.md)。

## <a name="onboard-to-larger-file-shares-standard-tier"></a>集成到较大的文件共享 (标准层)

本部分仅适用于标准文件共享。 所有高级文件共享都可用 100 TiB 作为 GA 产品/服务。

### <a name="restrictions"></a>限制

- Azure 预览版[条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)适用于处于预览阶段的大型文件共享, 包括与 Azure 文件同步部署一起使用。
- 要求您创建一个新的常规用途存储帐户 (无法扩展现有存储帐户)。
- 在将订阅接受到较大的文件共享预览后, 创建的任何新存储帐户都不能进行 LRS/ZRS 到 GRS 帐户转换。


### <a name="regional-availability"></a>适用区域

标准文件共享在所有区域中可用, 最高可达 5 TiB。 在某些区域, 可使用 100 TiB 限制, 下表列出了这些区域:

|地区 |支持的冗余 |支持现有的存储帐户 |门户支持 *   |
|-------|---------|---------|---------|
|澳大利亚东部  |LRS     |否    |是|
|法国中部  |LRS     |否    |尚不支持|
|法国南部    |LRS     |否    |尚不支持|
|东南亚  |LRS、ZRS|否    |是|
|西欧     |LRS、ZRS|否    |是|
|美国西部 2       |LRS、ZRS|否    |是|

\* 对于没有门户支持的区域, 你仍可以使用 PowerShell 或 Azure 命令行接口 (CLI) 来创建大于 5 TiB 的共享。 Altenatively, 在不指定配额的情况下通过门户创建新的共享。 这会创建默认大小为 100 TiB 的共享, 稍后可通过 PowerShell 或 Azure CLI 进行更新。

若要帮助我们确定新的区域和功能的优先级, 请填写此[调查](https://aka.ms/azurefilesatscalesurvey)。

### <a name="steps-to-onboard"></a>载入步骤

若要将订阅注册到较大的文件共享预览, 需要使用 Azure PowerShell。 您可以使用[Azure Cloud Shell](https://shell.azure.com/)或[本地安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps?view=azps-2.4.0)来运行以下 PowerShell 命令:

首先, 请确保选择要在预览中注册的订阅:

```powershell
$context = Get-AzSubscription -SubscriptionId ...
Set-AzContext $context
```

然后, 使用以下命令注册预览版:

```powershell
Register-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
运行这两个命令后, 会自动批准订阅。

若要验证你的注册状态, 你可以运行以下命令:

```powershell
Get-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
```

最长可能需要15分钟才能将状态更新为 "**已注册**"。 **注册**状态后, 应该能够使用此功能。

### <a name="use-larger-file-shares"></a>使用较大的文件共享

若要开始使用较大的文件共享, 请创建新的常规用途 v2 存储帐户和新的文件共享。

## <a name="data-growth-pattern"></a>数据增长模式

目前, Azure 文件共享的最大大小为 5 TiB 100 (TiB 为预览版)。 鉴于此当前限制，必须考虑部署 Azure 文件共享时的预期数据增长。

可使用 Azure 文件同步将多个 Azure 文件共享同步到单个 Windows 文件服务器。这可确保本地的较旧、大型文件共享能够导入到 Azure 文件同步。有关详细信息，请参阅[规划 Azure 文件同步部署](storage-files-planning.md)。

## <a name="data-transfer-method"></a>数据传输方法

可通过多种简单的选项将数据从现有文件共享（例如本地文件共享）批量传输到 Azure 文件。 几种常用选项包括（非详尽列表）：

* **Azure 文件同步**：在 Azure 文件共享（“云终结点”）和 Windows 目录命名空间（“服务器终结点”）之间首次同步期间，Azure 文件同步将把现有文件共享中的所有数据复制到 Azure 文件。
* **[Azure 导入/导出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：使用 Azure 导入/导出服务，可将硬盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure 文件共享。 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ：Robocopy 是 Windows 和 Windows Server 自带的一款知名复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：AzCopy 是一个命令行实用程序，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
