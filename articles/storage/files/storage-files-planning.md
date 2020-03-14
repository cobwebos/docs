---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b5bf5cc5c44226236f39a6e32c33ebe346e36eeb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269011"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署
可以通过两种主要方式部署[Azure 文件](storage-files-introduction.md)：直接装载无服务器 Azure 文件共享，或使用 Azure 文件同步在本地缓存 azure 文件共享。你选择哪种部署选项会更改你在规划部署时需要考虑的事项。 

- **直接装载 azure 文件共享**：由于 azure 文件提供 SMB 访问，你可以在本地或在云中使用 Windows、MacOS 和 Linux 中提供的标准 SMB 客户端装载 azure 文件共享。 由于 Azure 文件共享无服务器，因此在生产方案中部署不需要管理文件服务器或 NAS 设备。 这意味着无需应用软件修补程序或交换物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**： Azure 文件同步使你能够将组织的文件共享集中在 Azure 文件中，同时保持本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步将本地（或云） Windows Server 转换为 Azure 文件共享的快速缓存。 

本文主要介绍有关将 Azure 文件共享部署为直接由本地或云客户端装载的部署注意事项。 若要规划 Azure 文件同步部署，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)。

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

将 Azure 文件共享部署到存储帐户时，建议执行以下操作：

- 仅将 Azure 文件共享部署到具有其他 Azure 文件共享的存储帐户。 尽管 GPv2 存储帐户允许使用混合用途的存储帐户，但由于 Azure 文件共享等存储资源和 blob 容器共享存储帐户的限制，因此混合使用资源可能会导致更难进行故障排除以后会出现性能问题。 

- 部署 Azure 文件共享时，请注意存储帐户的 IOPS 限制。 理想情况下，会将文件共享1:1 映射到存储帐户，但这并非总是可能的，因为不同的限制和限制均来自于你的组织和 Azure。 如果不能在一个存储帐户中部署一个文件共享，则可以考虑哪些共享将会非常活跃，哪些共享将不活动，以确保不会将最热门话题的文件共享放在同一存储帐户中。

- 仅当在环境中找到 GPv2 和 FileStorage 帐户并升级 GPv1 和经典存储帐户时，才部署这些帐户。 

## <a name="identity"></a>标识
若要访问 Azure 文件共享，必须对文件共享的用户进行身份验证，并且有权访问该共享。 这是根据访问文件共享的用户的标识来完成的。 Azure 文件与三个主要标识提供者集成：
- **客户拥有的 Active Directory** （预览版）： Azure 存储帐户可以是加入到客户拥有的 windows server Active Directory 的域，就像 windows server 文件服务器或 NAS 设备一样。 可以在本地、在 Azure VM 中部署 Active Directory 域控制器，甚至可以在其他云提供程序中部署为 VM：Azure 文件与托管 DC 的位置无关。 存储帐户加入域后，最终用户可以使用登录到其 PC 的用户帐户装载文件共享。 基于 AD 的身份验证使用 Kerberos 身份验证协议。
- **Azure Active Directory 域服务（AZURE AD ds）** ： Azure AD ds 提供了可用于 Azure 资源的 Microsoft 托管的 Active Directory 域控制器。 将你的存储帐户加入到 Azure AD DS 的域可为加入到客户拥有的 Active Directory 的域提供类似的好处。 此部署选项最适用于需要基于 AD 的权限的应用程序提升和移动方案。 由于 Azure AD DS 提供了基于 AD 的身份验证，因此此选项还使用 Kerberos 身份验证协议。
- **Azure 存储帐户密钥**：还可以使用 azure 存储帐户密钥装载 azure 文件共享。 为了以这种方式装载文件共享，将使用存储帐户名称作为用户名，并使用存储帐户密钥作为密码。 使用存储帐户密钥装载 Azure 文件共享实际上是一项管理员操作，因为已装载的文件共享对共享上的所有文件和文件夹都具有完全权限，即使这些文件和文件夹具有 Acl 也是如此。 使用存储帐户密钥通过 SMB 装载时，使用 NTLMv2 身份验证协议。

对于从本地文件服务器迁移的客户，或在 Azure 文件中创建新的文件共享以与 Windows 文件服务器或 NAS 设备的行为类似，建议选择将存储帐户加入到**客户拥有的 Active Directory**域。 若要详细了解有关将存储帐户加入到客户拥有的 Active Directory 的域，请参阅[Azure 文件 Active Directory 概述](storage-files-active-directory-overview.md)。

如果要使用存储帐户密钥来访问 Azure 文件共享，我们建议使用 "[网络](#networking)" 部分中所述的服务终结点。

## <a name="networking"></a>网络
可以从任何位置通过存储帐户的公共终结点访问 Azure 文件共享。 这意味着，经过身份验证的请求（如用户的登录标识所授权的请求）可从 Azure 内部或外部发起。 在许多客户环境中，本地工作站上的 Azure 文件共享的初始装载会失败，即使 Azure Vm 的装载成功。 这样做的原因是许多组织和 internet 服务提供商（Isp）会阻止 SMB 用来通信的端口，即端口445。 若要概览允许或不允许从端口 445 访问的 ISP，请转到 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

若要取消阻止对 Azure 文件共享的访问，有两个主要选项：

- 为组织的本地网络取消阻止端口445。 Azure 文件共享仅可通过公共终结点使用 SMB 3.0 和 FileREST API 等 internet 安全协议进行外部访问。 这是从本地访问 Azure 文件共享的最简单方法，因为它不需要在更改组织的出站端口规则的情况下进行高级网络配置，但建议删除旧的和弃用的 SMB 版本协议，即 SMB 1.0。 若要了解如何执行此操作，请参阅[保护 Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)和[保护 Linux](storage-how-to-use-files-linux.md#securing-linux)。

- 通过 ExpressRoute 或 VPN 连接访问 Azure 文件共享。 通过网络隧道访问 Azure 文件共享时，可以装载 Azure 文件共享，如本地文件共享，因为 SMB 流量不遍历组织边界。   

尽管从技术角度来看，通过公共终结点装载 Azure 文件共享相当容易，但我们希望大多数客户选择通过 ExpressRoute 或 VPN 连接来装载其 Azure 文件共享。 为此，你将需要为你的环境配置以下各项：  

- **使用 ExpressRoute、站点到站点或点到站点 VPN 的网络隧道：通过**隧道进入虚拟网络，可从本地访问 Azure 文件共享，即使阻止端口445也是如此。
- **专用终结点**：专用终结点在虚拟网络的地址空间中为存储帐户指定一个专用的 IP 地址。 这使得网络隧道无需打开本地网络，直到 Azure 存储群集所拥有的所有 IP 地址范围。 
- **DNS 转发**：配置本地 DNS 以解析存储帐户的名称（即，为公有云区域 `storageaccount.file.core.windows.net`），将其解析为专用终结点的 IP 地址。

若要规划与部署 Azure 文件共享相关联的网络，请参阅[Azure 文件网络注意事项](storage-files-networking-overview.md)。

## <a name="encryption"></a>加密
Azure 文件支持两种不同类型的加密：传输过程中的加密，它们与装载/访问 Azure 文件共享时使用的加密有关，后者与数据存储在磁盘上时加密的方式相关。 

### <a name="encryption-in-transit"></a>传输中加密
默认情况下，所有 Azure 存储帐户均已启用传输中加密。 即通过 SMB 装载文件共享或通过 FileREST 协议（例如，通过 Azure门户、PowerShell/CLI 或 Azure SDK）访问文件共享时，Azure 文件存储仅允许通过加密或 HTTPS 使用 SMB 3.0 及更高版本建立的连接。 如果启用了传输中加密，则不支持 SMB 3.0 的客户端或支持 SMB 3.0 但不支持 SMB 加密的客户端将无法装载 Azure 文件共享。 要详细了解哪些操作系统支持具有加密功能的 SMB 3.0，请参阅适用于 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的详细文档。 PowerShell、CLI 和 SDK 的所有当前版本均支持 HTTPS。  

可以为 Azure 存储帐户禁用传输中加密。 禁用加密时，Azure 文件还将允许 SMB 2.1、不进行加密的 SMB 3.0 和通过 HTTP 进行的未加密 FileREST API 调用。 禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行的旧版应用程序。 Azure 文件存储仅允许在与 Azure 文件共享相同的 Azure 区域内建立 SMB 2.1 连接；Azure 文件共享的 Azure 区域之外的 SMB 2.1 客户端（例如，本地或其他 Azure 区域）将无法访问文件共享。

强烈建议确保启用传输中数据的加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>静态加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

通常，高级文件共享和标准文件共享之间的 Azure 文件功能和与其他服务的互操作性是相同的，但有几个重要的区别：
- **计费模式**
    - 高级文件共享使用预配的计费模式进行计费，这意味着你需要为预配的存储量而不是实际请求的存储量付费。 
    - 标准文件共享使用即用即付模型进行计费，其中包括实际使用的存储量的基本成本，并根据使用共享的方式增加事务成本。 使用标准文件共享时，如果你使用（读取/写入/装载） Azure 文件共享，则你的帐单将会增加。
- **冗余选项**
    - 高级文件共享仅适用于本地冗余（LRS）和区域冗余（ZRS）存储。 
    - 标准文件共享可用于本地冗余、区域冗余、异地冗余（GRS）和异地冗余（GZRS）存储。
- **文件共享的最大大小**
    - 高级文件共享最多可预配到 100 TiB，无需任何额外的工作。
    - 默认情况下，标准文件共享仅可跨越最多5个 TiB，不过，通过选择 "*大文件共享*存储帐户" 功能标志，可以将共享限制增加到 100 TiB。 对于本地冗余存储帐户或区域冗余存储帐户，标准文件共享最多只能跨越100个 TiB。 有关提高的详细信息  
- **区域可用性**
    - 高级文件共享在每个区域中都不可用，区域冗余支持在一小部分区域中提供。 若要确定高级文件共享当前是否在你的区域中可用，请参阅 Azure 的 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)" 页。 若要找出哪些区域支持 ZRS，请参阅[按区域的 Azure 可用性区域支持](../../availability-zones/az-overview.md#services-support-by-region)。 若要帮助我们确定新的区域和高级层功能的优先级，请填写此[调查](https://aka.ms/pfsfeedback)。
    - 标准文件共享在每个 Azure 区域中可用。
- Azure Kubernetes Service （AKS）支持版本1.13 及更高版本中的高级文件共享。

一旦将文件共享创建为高级或标准文件共享，就不能自动将其转换为另一层。 如果要切换到另一层，则必须在该层中创建新的文件共享，并手动将原始共享中的数据复制到所创建的新共享。 建议使用适用于 Windows 的 `robocopy` 或用于 macOS 和 Linux 的 `rsync` 来执行该复制。

### <a name="understanding-provisioning-for-premium-file-shares"></a>了解高级文件共享的预配
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

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>启用标准文件共享，跨越多达 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>区域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>迁移
在许多情况下，你不会为你的组织建立 net 全新文件共享，而是将现有文件共享从本地文件服务器或 NAS 设备迁移到 Azure 文件。 Microsoft 和第三方均提供了许多工具来执行到文件共享的迁移，但它们大致分为两个类别：

- **维护文件系统属性（如 acl 和时间戳）的工具**：
    - **[Azure 文件同步](storage-sync-files-planning.md)** ： Azure 文件同步可用作将数据引入到 Azure 文件共享的方法，即使所需的最终部署不会保留本地状态也是如此。 可以在现有的 Windows Server 2012 R2、Windows Server 2016 和 Windows Server 2019 部署上就地安装 Azure 文件同步。 使用 Azure 文件同步作为摄取机制的一个优点是，最终用户可以继续使用现有的文件共享;在后台上传完所有数据之后，可以对 Azure 文件共享进行剪切。
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ： Robocopy 是 Windows 和 windows Server 随附的众所周知的复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。

- **不维护文件系统属性的工具**：
    - **Data Box**： Data Box 提供将物理数据传输到 Azure 的脱机数据传输机制。 此方法旨在提高吞吐量和节省带宽，但目前不支持文件系统属性，例如时间戳和 Acl。
    - **AzCopy[：AzCopy 是一个命令行实用工具，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** 。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
