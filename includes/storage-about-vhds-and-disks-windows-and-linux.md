---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4d208ca28f6287489f104ba4e2ea9696e7a1f58
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
---
## <a name="about-vhds"></a>关于 VHD

Azure 中使用的 VHD 是在 Azure 的标准或高级存储帐户中作为页 Blob 存储的 .vhd 文件。 有关页 Blob 的详细信息，请参阅[了解块 Blob 和页 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/)。 有关高级存储的详细信息，请参阅[高性能高级存储和 Azure VM](../articles/virtual-machines/windows/premium-storage.md)。

Azure 支持固定的磁盘 VHD 格式。 固定格式在文件内对逻辑磁盘以线性方式布局，使磁盘偏移量 X 存储在 Blob 偏移量 X 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 通常，由于大多数磁盘中都有较大的未使用区域，因此固定格式会浪费空间。 不过，Azure 以稀疏格式存储 .vhd 文件，因此可兼获固定和动态格式磁盘的优点。 有关更多详细信息，请参阅[虚拟硬盘入门](https://technet.microsoft.com/library/dd979539.aspx)。

Azure 中想要用作源创建磁盘或映像的所有 .vhd 文件都是只读的，但用户已上传或复制到 Azure 存储的 .vhd 文件除外（可以是读写的或只读的）。 创建磁盘或映像时，Azure 将生成源 .vhd 文件的副本。 这些副本可以是只读文件，也可以是读写文件，具体取决于使用 VHD 的方式。

基于映像创建虚拟机时，Azure 将为虚拟机创建磁盘，该磁盘是源 .vhd 文件的副本。 为避免被意外删除，Azure 对任何用于创建映像、操作系统磁盘或数据磁盘的源 .vhd 文件设置了租约。

在删除源 .vhd 文件之前，需要先通过删除磁盘或映像来解除租约。 若要删除由虚拟机当前用作操作系统磁盘的 .vhd 文件，可以通过删除虚拟机并删除所有关联的磁盘，一次性删除虚拟机、操作系统磁盘和源 .vhd 文件。 但是，删除作为数据磁盘源的 .vhd 文件需要按一定顺序执行几个步骤。 首先从虚拟机分离该磁盘，然后删除该磁盘，再删除 .vhd 文件。
> [!WARNING]
> 如果从存储空间删除了源 .vhd 文件或删除了存储帐户，Microsoft 无法恢复数据。
> 
> 根据设计，高级存储中的页 Blob 只用作 VHD。 Microsoft 建议不要在高级存储的页 Blob 中存储其他类型的数据，因为成本可能显著过高。 使用块 Blob 存储不在 VHD 中的数据。

## <a name="types-of-disks"></a>磁盘类型 

Azure 磁盘具备 99.999% 的可用性。 Azure 磁盘持续提供企业级持久性，年化故障率为 0%，达到行业领先水平。

在创建磁盘时，有两种适用于存储的性能层可供选择 -- 标准存储和高级存储。 另外还有两类磁盘 -- 非托管磁盘和托管磁盘 -- 这两类磁盘可以驻留在任一性能层中。


### <a name="standard-storage"></a>标准存储 

标准存储受 HDD 支持，可以在确保性能的同时提供经济高效的存储。 标准存储可以在一个数据中心进行本地复制，也可以通过主要数据中心和辅助数据中心实现异地冗余。 有关存储复制的详细信息，请参阅 [Azure 存储复制](../articles/storage/common/storage-redundancy.md)。 

要详细了解如何将标准存储与 VM 磁盘结合使用，请参阅[标准存储和磁盘](../articles/virtual-machines/windows/standard-storage.md)。

### <a name="premium-storage"></a>高级存储 

高级存储受 SSD 支持，为运行 I/O 密集型工作负荷的 VM 提供高性能、低延迟的磁盘支持。 通常可以使用其大小在系列名称中包含“s”的高级存储。 例如，有 Dv3 系列和 Dsv3 系列，可以将 Dsv3 系列用于高级存储。  有关详细信息，请参阅[高级存储](../articles/virtual-machines/windows/premium-storage.md)。

### <a name="unmanaged-disks"></a>非托管磁盘

非托管磁盘是一直被 VM 使用的传统类型的磁盘。 有了这些以后，即可创建自己的存储帐户并在创建磁盘时指定该存储帐户。 必须确保不将太多磁盘置于同一存储帐户中，因为可能会超过存储帐户的[伸缩性目标](../articles/storage/common/storage-scalability-targets.md)（例如 20,000 IOPS），导致 VM 数受限。 使用非托管磁盘时，必须确定如何最大程度地使用一个或多个存储帐户，以便充分利用 VM 的性能。

### <a name="managed-disks"></a>托管磁盘 

托管磁盘为用户在后台处理存储帐户的创建/管理，确保用户不需担心存储帐户的可伸缩性限制。 用户只需指定磁盘大小和性能层（标准/高级），Azure 就会为用户创建和管理磁盘。 即使在添加磁盘或者对 VM 进行上下伸缩的时候，用户也不需担心所使用的存储。 

用户还可以按 Azure 区域在一个存储帐户中管理自定义 映像，并使用这些映像在同一订阅中创建数百台 VM。 有关管理磁盘的详细信息，请参阅[托管磁盘概述](../articles/virtual-machines/windows/managed-disks-overview.md)。

建议为新 VM 使用 Azure 托管磁盘，并将之前的非托管磁盘转换为托管磁盘，以便充分利用托管磁盘中提供的多项功能。

### <a name="disk-comparison"></a>磁盘比较

下表针对托管磁盘和非托管磁盘比较了高级和标准两个性能层，方便用户确定要使用的具体层。

|    | Azure 高级磁盘 | Azure 标准磁盘 |
|--- | ------------------ | ------------------- |
| 磁盘类型 | 固态硬盘 (SSD) | 硬盘驱动器 (HDD)  |
| 概述  | 基于 SSD 的高性能、低延迟磁盘支持，适用于运行 IO 密集型工作负荷或托管任务关键型生产环境的 VM | 基于 HDD 的经济高效型磁盘支持，适用于开发/测试 VM 方案 |
| 场景  | 生产和性能敏感型工作负荷 | 开发/测试, 非关键, <br>很少访问 |
| 磁盘大小 | P4：32 GB（仅托管磁盘）<br>P6：64 GB（仅托管磁盘）<br>P10：128 GB<br>P20：512 GB<br>P30：1024 GB<br>P40：2048 GB<br>P50：4095 GB | 非托管磁盘：1 GB - 4 TB (4095 GB) <br><br>托管磁盘：<br> S4：32 GB <br>S6：64 GB <br>S10：128 GB <br>S20：512 GB <br>S30：1024 GB <br>S40：2048 GB<br>S50：4095 GB| 
| 每个磁盘的最大吞吐量 | 250 MB/秒 | 60 MB/秒 | 
| 每个磁盘的最大 IOPS | 7,500 IOPS | 500 IOPS | 

