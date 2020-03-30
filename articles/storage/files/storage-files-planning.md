---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502433"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署
[Azure 文件](storage-files-introduction.md)可通过两种主要方式进行部署：直接安装无服务器 Azure 文件共享或使用 Azure 文件同步在本地缓存 Azure 文件共享。您选择的部署选项会更改在规划部署时需要考虑的事项。 

- **Azure 文件共享的直接装载**：由于 Azure 文件提供 SMB 访问，因此可以使用 Windows、macOS 和 Linux 中可用的标准 SMB 客户端在本地或云中装载 Azure 文件共享。 由于 Azure 文件共享是无服务器的，因此为生产方案部署不需要管理文件服务器或 NAS 设备。 这意味着您不必应用软件修补程序或交换物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**：Azure 文件同步使您能够在 Azure 文件中集中组织的文件共享，同时保持本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步将本地（或云）Windows 服务器转换为 Azure 文件共享的快速缓存。 

本文主要讨论部署由本地或云客户端直接装载的 Azure 文件共享的部署注意事项。 要规划 Azure 文件同步部署，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)。

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

将 Azure 文件共享部署到存储帐户时，我们建议：

- 仅将 Azure 文件共享部署到具有其他 Azure 文件共享的存储帐户中。 尽管 GPv2 存储帐户允许您具有混合用途存储帐户，但由于存储资源（如 Azure 文件共享和 Blob 容器）共享存储帐户的限制，因此将资源混合在一起可能会加大故障排除的难度稍后，性能问题。 

- 在部署 Azure 文件共享时，注意存储帐户的 IOPS 限制。 理想情况下，您将文件共享 1：1 映射到存储帐户，但由于组织与 Azure 的各种限制和限制，这并不总是可能的。 如果不可能在一个存储帐户中只部署一个文件共享，请考虑哪些共享将高度活跃，哪些共享将不太活跃，以确保最热门的文件共享不会放在同一个存储帐户中。

- 仅在环境中找到 GPv2 和文件存储帐户并升级 GPv1 和经典存储帐户。 

## <a name="identity"></a>标识
要访问 Azure 文件共享，必须对文件共享的用户进行身份验证并具有访问共享的授权。 这是基于访问文件共享的用户的标识完成的。 Azure 文件与三个主要标识提供程序集成：
- **客户拥有的活动目录**（预览）：Azure 存储帐户可以像 Windows 服务器文件服务器或 NAS 设备一样，加入到客户拥有的 Windows Server 活动目录的域。 活动目录域控制器可以部署在本地、Azure VM 中，甚至可以作为其他云提供商中的 VM 进行部署;Azure 文件与 DC 的托管位置无关。 加入存储帐户后，最终用户就可以将文件共享与登录到其 PC 中的用户帐户一起装载。 基于 AD 的身份验证使用 Kerberos 身份验证协议。
- **Azure 活动目录域服务 （Azure AD DS）：** Azure AD DS 提供 Microsoft 管理的活动目录域控制器，可用于 Azure 资源。 将存储帐户加入 Azure AD DS 的域与将存储帐户加入客户拥有的 Active Directory 的域提供了类似的好处。 此部署选项对于需要基于 AD 的权限的应用程序升降和移位方案最有用。 由于 Azure AD DS 提供基于 AD 的身份验证，此选项还使用 Kerberos 身份验证协议。
- **Azure 存储帐户密钥**：Azure 文件共享也可以装载 Azure 存储帐户密钥。 要以这种方式装载文件共享，存储帐户名称用作用户名，存储帐户密钥用作密码。 使用存储帐户密钥装载 Azure 文件共享实际上是管理员操作，因为装载的文件共享将对共享上的所有文件和文件夹具有完全权限，即使它们具有 ACL。 当使用存储帐户密钥装载到 SMB 上时，将使用 NTLMv2 身份验证协议。

对于从本地文件服务器迁移的客户，或在 Azure 文件中创建新的文件共享，以像 Windows 文件服务器或 NAS 设备一样，建议选择将存储帐户加入**客户拥有的 Active Directory 的**域。 要了解有关域将存储帐户加入客户拥有的活动目录的详细信息，请参阅[Azure 文件活动目录概述](storage-files-active-directory-overview.md)。

如果要使用存储帐户密钥访问 Azure 文件共享，我们建议使用["网络](#networking)"部分中所述的服务终结点。

## <a name="networking"></a>网络
Azure 文件共享可通过存储帐户的公共终结点从任何位置访问。 这意味着经过身份验证的请求（如用户登录标识授权的请求）可以安全地源自 Azure 的内部和外部。 在许多客户环境中，即使 Azure VM 的装载成功，本地工作站上 Azure 文件共享的初始装载也会失败。 原因是许多组织和 Internet 服务提供商 （ISP） 阻止 SMB 用于通信的端口端口 445。 若要概览允许或不允许从端口 445 访问的 ISP，请转到 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

要取消阻止对 Azure 文件共享的访问，有两个主要选项：

- 为组织的本地网络取消阻止端口 445。 Azure 文件共享只能通过公共终结点使用 Internet 安全协议（如 SMB 3.0 和 FileREST API）进行外部访问。 这是从本地访问 Azure 文件共享的最简单方法，因为它除了更改组织的出站端口规则之外不需要高级网络配置，但是，我们建议您删除 SMB 的旧版本和弃用版本协议，即 SMB 1.0。 要了解如何执行此操作，请参阅[保护 Windows/Windows 服务器](storage-how-to-use-files-windows.md#securing-windowswindows-server)和保护[Linux](storage-how-to-use-files-linux.md#securing-linux)。

- 通过 ExpressRoute 或 VPN 连接访问 Azure 文件共享。 通过网络隧道访问 Azure 文件共享时，可以像本地文件共享一样装载 Azure 文件共享，因为 SMB 流量不会遍历组织边界。   

尽管从技术角度来说，通过公共终结点装载 Azure 文件共享要容易得多，但我们预计大多数客户将选择通过 ExpressRoute 或 VPN 连接装载其 Azure 文件共享。 为此，您需要为环境配置以下内容：  

- **使用 ExpressRoute、站点到站点或点到站点 VPN**的网络隧道：隧道到虚拟网络允许从本地访问 Azure 文件共享，即使端口 445 被阻止也是如此。
- **专用终结点**：专用终结点从虚拟网络的地址空间内为您的存储帐户提供专用 IP 地址。 这支持网络隧道，而无需打开本地网络，最多满足 Azure 存储群集拥有的所有 IP 地址范围。 
- **DNS 转发**：配置本地 DNS 以解析存储帐户的名称（即`storageaccount.file.core.windows.net`公共云区域），以解析为专用终结点的 IP 地址。

要规划与部署 Azure 文件共享关联的网络，请参阅[Azure 文件网络注意事项](storage-files-networking-overview.md)。

## <a name="encryption"></a>加密
Azure 文件支持两种不同类型的加密：传输中的加密（与装载/访问 Azure 文件共享时使用的加密相关）和静态加密，与数据存储在磁盘上时数据的加密方式有关。 

### <a name="encryption-in-transit"></a>传输中加密
默认情况下，所有 Azure 存储帐户均已启用传输中加密。 即通过 SMB 装载文件共享或通过 FileREST 协议（例如，通过 Azure门户、PowerShell/CLI 或 Azure SDK）访问文件共享时，Azure 文件存储仅允许通过加密或 HTTPS 使用 SMB 3.0 及更高版本建立的连接。 如果启用了传输中加密，则不支持 SMB 3.0 的客户端或支持 SMB 3.0 但不支持 SMB 加密的客户端将无法装载 Azure 文件共享。 要详细了解哪些操作系统支持具有加密功能的 SMB 3.0，请参阅适用于 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的详细文档。 PowerShell、CLI 和 SDK 的所有当前版本均支持 HTTPS。  

可以为 Azure 存储帐户禁用传输中加密。 禁用加密后，Azure 文件还将允许 SMB 2.1、SMB 3.0 不加密，以及通过 HTTP 进行未加密的文件 REST API 调用。 禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行的旧版应用程序。 Azure 文件存储仅允许在与 Azure 文件共享相同的 Azure 区域内建立 SMB 2.1 连接；Azure 文件共享的 Azure 区域之外的 SMB 2.1 客户端（例如，本地或其他 Azure 区域）将无法访问文件共享。

我们强烈建议确保启用传输中数据的加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>静态加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

通常，Azure 文件功能与其他服务的互操作性在高级文件共享和标准文件共享之间是相同的，但是有一些重要区别：
- **计费模式**
    - 高级文件共享使用预配计费模型计费，这意味着您需要为预配的存储量而不是实际要求多少存储付费。 
    - 标准文件共享使用即用即付模型计费，该模型包括实际消耗的存储量的基本存储成本，然后根据您如何使用共享来支付额外的事务成本。 使用标准文件共享时，如果使用 Azure 文件共享更多内容，则帐单将增加。
- **冗余选项**
    - 高级文件共享仅适用于本地冗余 （LRS） 和区域冗余 （ZRS） 存储。 
    - 标准文件共享可用于本地冗余、区域冗余、地理冗余 （GRS） 和地理区域冗余 （GZRS） 存储。
- **文件共享的最大大小**
    - 高级文件共享可以预配多达 100 TiB，而无需任何其他工作。
    - 默认情况下，标准文件共享只能跨越 5 TiB，尽管通过选择进入*大型文件共享*存储帐户功能标志，共享限制可以增加到 100 TiB。 对于本地冗余或区域冗余存储帐户，标准文件共享可能最多只能跨越 100 TiB。 有关增加文件共享大小的详细信息，请参阅[启用和创建大型文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)。
- **区域可用性**
    - 高级文件共享并非在每个区域都可用，区域冗余支持在较小的区域子集中可用。 若要确定高级文件共享目前是否可在你的区域中使用，请参阅 Azure 的[产品的上市区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)页。 要了解哪些区域支持 ZRS，请参阅[Azure 可用性区域支持区域](../../availability-zones/az-overview.md#services-support-by-region)。 为了帮助我们确定新区域和高级层功能的优先级，请填写此[调查](https://aka.ms/pfsfeedback)。
    - 标准文件共享在每个 Azure 区域中都可用。
- Azure 库伯奈斯服务 （AKS） 支持版本 1.13 及更高版本中的高级文件共享。

一旦文件共享创建为高级文件共享或标准文件共享，则无法自动将其转换为其他层。 如果要切换到其他层，则必须在该层中创建新的文件共享，并手动将数据从原始共享复制到您创建的新共享。 我们建议使用`robocopy`Windows 或`rsync`macOS 和 Linux 来执行该副本。

### <a name="understanding-provisioning-for-premium-file-shares"></a>了解高级文件共享的预配
高级文件共享是基于固定的 GiB/IOPS/吞吐量比率预配的。 对于预配的每个 GiB，将向该共享分配 1 IOPS 和 0.1 MiB/秒的吞吐量，最多可达每个共享的最大限制。 允许的最小预配为 100 GiB 以及最小的 IOPS/吞吐量。

最大限度地提供服务时，对于预配的存储，所有共享都可以突增到每 GiB 3 IOPS，持续 60 分钟或更长时间，具体取决于共享大小。 新共享将根据预配的容量以完全突增额度开始。

必须以 1 GiB 为增量预配共享。 最小大小为 100 GiB，下一大小为 101 GiB，依此类推。

> [!TIP]
> 基线 IOPS = 1 * 预配的 GiB。 （最大可为 100,000 IOPS）。
>
> 突发限制 = 3 * 基准 IOPS。 （最大可为 100,000 IOPS）。
>
> 出口速率 = 60 MiB/秒 + 0.06 * 预配的 GiB
>
> 入口速率 = 40 MiB/秒 + 0.04 * 预配的 GiB

预配的共享大小按共享配额指定。 随时可以提高共享配额，但只能在自上次提高后的 24 小时之后降低配额。 等待 24 小时且不要提高配额，然后，可将共享配额降低任意次数，直到再次提高配额为止。 IOPS/吞吐量规模更改将在大小更改后的数分钟内生效。

可将预配共享的大小减至所用 GiB 以下。 这样做不会丢失数据，但仍会根据所用大小计费，并且性能（基线 IOPS、吞吐量和突发 IOPS）与预配的共享（而不是所用大小）相符。

下表演示了这些预配共享大小公式的几个示例：

|容量 (GiB) | 基线 IOPS | 突发 IOPS | 出口速率（MiB/秒） | 入口速率（MiB/秒） |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | 最大 1,500   | 90   | 60   |
|1,024       | 1,024   | 最大 3,072   | 122   | 81   |
|5,120       | 5,120   | 最大 15,360  | 368   | 245   |
|10,240      | 10,240  | 最大 30,720  | 675 | 450   |
|33,792      | 33,792  | 最大 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | 最大 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | 最大 100,000 | 6,204 | 4,136   |

> [!NOTE]
> 文件共享性能与计算机网络限制、可用网络带宽、IO 大小、并行度和其他许多因素相关。 例如，基于具有 8 个 KiB 读/写 IO 大小的内部测试，单个 Windows 虚拟机 *"标准F16s_v2"* 连接到 SMB 上的高级文件共享可以实现 20K 读取 IOPS 和 15K 写入 IOPS。 使用 512 MiB 读取/写入 IO 大小，相同的 VM 可实现 1.1 GiB/s 出口和 370 MiB/s 入口吞吐量。 若要实现最大性能规模，请将负载分散到多个 VM。 有关一些常见性能问题和解决方法，请参阅[故障排除指南](storage-troubleshooting-files-performance.md)。

#### <a name="bursting"></a>突发
高级文件共享最大可按系数 3 突发其 IOPS。 突发是自动进行的，根据额度系统运行。 突发采用“尽力而为”的原则，突发限制没有保证，文件共享只能在限制范围内突发。**

每当文件共享的流量低于基线 IOPS 时，额度将累积在突发桶中。 例如，100 GiB 共享有 100 个基线 IOPS。 如果共享中的实际流量在特定 1 秒间隔内为 40 IOPS，则会将 60 个未使用的 IOPS 贷记到突发桶。 以后在操作超过基线 IOPS 时，将使用这些额度。

> [!TIP]
> 突发桶的大小 = 基线 IOPS * 2 * 3600。

每当共享超过基线 IOPS 并且在突发桶中具有额度，就会突发。 只要有剩余的额度，共享就可继续突发，不过，小于 50 TiB 的共享最多只能有一小时不会超过突发限制。 大于 50 TiB 的共享在技术上可以超过此一小时限制（最长可达到两小时），但这取决于应计的突发额度数。 超出基线 IOPS 的每个 IO 会消耗一个额度，一旦耗尽所有额度，共享就会恢复为基线 IOPS。

共享额度具有三种状态：

- 应计：文件共享使用的 IOPS 小于基线 IOPS。
- 下降：文件共享正在突发。
- 保持平稳：没有额度，或正在使用基线 IOPS。

新文件共享最初在其突发桶中包含所有额度。 如果由于服务器的限制，导致共享 IOPS 低于基线 IOPS，则不会对突发额度进行应计。

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>使标准文件共享跨越多达 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>区域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>迁移
在许多情况下，您不会为组织建立净新文件共享，而是将现有文件共享从本地文件服务器或 NAS 设备迁移到 Azure 文件。 有许多工具（由 Microsoft 和第三方提供）用于对文件共享进行迁移，但它们大致可以分为两类：

- **维护文件系统属性（如 ACL 和时间戳）的工具**：
    - **[Azure 文件同步](storage-sync-files-planning.md)**：Azure 文件同步可用作将数据引入 Azure 文件共享的方法，即使所需的结束部署不是维护本地状态也是如此。 Azure 文件同步可以安装在现有的 Windows 服务器 2012 R2、Windows 服务器 2016 和 Windows 服务器 2019 部署上。 使用 Azure 文件同步作为引入机制的一个优点是，最终用户可以继续使用现有的文件共享;在后台完成所有数据上载后，可能会将共享到 Azure 文件共享。
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**： Robocopy 是一个众所周知的复制工具，随 Windows 和 Windows 服务器一起提供。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。

- **不维护文件系统属性的工具**：
    - **数据框**：数据框提供脱机数据传输机制，将数据发送到 Azure。 此方法旨在增加吞吐量和节省带宽，但当前不支持文件系统属性（如时间戳和 ACL）。
    - [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)：AzCopy 是一个命令行实用工具，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据****。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
