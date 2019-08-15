---
title: include 文件
description: include 文件
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 529a8b6136a5d9c69b044df2614644bdbd4fd4f4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012042"
---
共享映像库是一种可以帮助你围绕托管映像构建结构和组织的服务。 共享映像库提供以下功能：

- 对映像进行托管式全局复制。
- 对映像进行版本控制和分组，以便于管理。
- 具有区域冗余存储 (ZRS) 帐户的高度可用映像, 位于支持可用性区域的区域中。 ZRS 可以更好地恢复映像，防止区域性故障。
- 使用 RBAC 跨订阅以及甚至在 Active Directory (AD) 租户之间共享。
- 使用每个区域中的映像副本缩放部署。

使用共享映像库，可以将映像共享给组织内的不同用户、服务主体或 AD 组。 共享映像可以复制到多个区域，以便更快地扩展部署。

托管映像是完整 VM（包括任何附加的数据磁盘）的副本或者只是 OS 磁盘的副本，具体取决于映像的创建方式。 从映像创建 VM 时，将使用该映像中的 VHD 副本来为新 VM 创建磁盘。 托管映像保留在存储中，可反复用来创建新的 VM。

如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地共享映像。 

共享映像库功能具有多种资源类型：

| Resource | 描述|
|----------|------------|
| **托管映像** | 一个基本映像，可以单独使用，也可用于在映像库中创建**映像版本**。 托管映像是从通用 VM 创建的。 托管映像是一种特殊的 VHD 类型，可用于生成多个 VM，并且现在可用于创建共享映像版本。 |
| **映像库** | 与 Azure 市场一样，**映像库**是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像在库中定义，携带有关该映像及其在组织内部使用的要求的信息。 可以包含映像是 Windows 还是 Linux、最小和最大内存要求以及发行说明等信息。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用**映像版本**来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用**映像版本**创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |

<br>


![演示如何在库中创建多个映像版本的示意图](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>映像定义

映像定义是映像版本的逻辑分组。 映像定义包含有关创建映像的原因、它适用于哪个 OS 的信息，以及映像的用法信息。 映像定义类似于一个计划，提供了有关如何创建特定映像的所有详细信息。 不要从映像定义部署 VM，而要从基于该定义创建的映像版本部署 VM。


对于每个映像定义，将组合使用“发布者”、“套餐”和“SKU”这三个参数。 这些参数用于查找特定的映像定义。 可以拥有共享一个或两个但不是全部三个值的映像版本。  例如，以下是三个映像定义及其值：

|映像定义|发布服务器|套餐|Sku|
|---|---|---|---|
|myImage1|Contoso|财经|后端|
|myImage2|Contoso|财经|前端|
|myImage3|正在测试|财经|前端|

所有这三个映像都有唯一的一组值。 格式类似于当前在 Azure PowerShell 中为 [Azure 市场映像](../articles/virtual-machines/windows/cli-ps-findimage.md)指定发布者、套餐和 SKU，以获取最新市场映像版本的方式。 每个映像定义需要包含一组唯一的这些值。

下面是可以在映像定义中设置的其他参数，它们可让你更轻松地跟踪自己的资源：

* 操作系统状态 - 可将 OS 状态设置为通用化或专用化，但目前仅支持通用化。 必须从已使用适用于 Windows 的 Sysprep 或适用于 Linux 的 `waagent -deprovision` 通用化的 VM 创建映像。
* 操作系统 - 可以是 Windows 或 Linux。
* 说明 - 使用说明可以更详细地解释该映像定义为何存在。 例如，可为预装了应用程序的前端服务器创建一个映像定义。
* Eula - 可用于指向特定于映像定义的最终用户许可协议。
* 隐私声明和发行说明 - 在 Azure 存储中存储发行说明和隐私声明，并提供一个 URI 用于访问这些信息（作为映像定义的一部分）。
* 生命周期终结日期 - 将生命周期终结日期附加到映像定义，以便能够使用自动化来删除旧的映像定义。
* 标记 - 创建映像定义时可以添加标记。 有关标记的详细信息，请参阅[使用标记来组织资源](../articles/azure-resource-manager/resource-group-using-tags.md)。
* 最小和最大 vCPU 与内存建议量 - 如果映像附带 vCPU 和内存建议量，则你可以将该信息附加到映像定义。
* 不允许的磁盘类型 - 可以提供有关 VM 所需存储的信息。 例如，如果映像不适合用于标准 HDD 磁盘，请将此类磁盘添加到禁止列表。


## <a name="regional-support"></a>区域支持

下表列出了源区域。 所有公共区域都可以是目标区域, 但是若要复制到澳大利亚中部和澳大利亚中部 2, 需要订阅白名单。 要请求允许列表，请转到： https://azure.microsoft.com/global-infrastructure/australia/contact/


| 源区域 |
|---------------------|-----------------|------------------|-----------------|
| 澳大利亚中部   | 美国中部 EUAP | 韩国中部    | 美国中西部 |
| 澳大利亚中部 2 | 东亚       | 韩国      | 西欧     |
| 澳大利亚东部      | East US         | 美国中北部 | 印度西部      |
| 澳大利亚东南部 | 美国东部 2       | 北欧     | 美国西部         |
| 巴西南部        | 美国东部 2 EUAP  | 美国中南部 | 美国西部 2       |
| 加拿大中部      | 法国中部  | 印度南部      |                 |
| 加拿大东部         | 法国南部    | 东南亚   |                 |
| 印度中部       | 日本东部      | 英国南部         |                 |
| 美国中部          | 日本西部      | 英国西部          |                 |



## <a name="limits"></a>限制 

使用共享映像库部署资源时，每个订阅存在限制：
- 每个区域的每个订阅限制为 100 个共享映像库
- 每个区域的每个订阅限制为 1,000 个映像定义
- 每个区域的每个订阅限制为 10,000 个映像版本

有关详细信息，请参阅[根据限制检查资源用量](https://docs.microsoft.com/azure/networking/check-usage-against-limits)，其中提供了有关如何检查当前用量的示例。
 

## <a name="scaling"></a>缩放
使用共享映像库可以指定要让 Azure 保留的映像副本数。 这有助于实现多 VM 部署方案，因为可将 VM 部署分散到不同的副本，减少单个副本过载导致实例创建过程受到限制的可能性。


现在，使用共享映像库，最多可在虚拟机规模集中部署 1,000 个 VM 实例（相比使用托管映像部署 600 个有所增加）。 映像副本可用于提高部署性能、可靠性和一致性。  可以在每个目标区域中设置不同的副本计数，具体视该区域的缩放需求而定。 由于每个副本是映像的深层复制，因此，这有助于使用每个额外的副本线性地缩放部署。 虽然我们不了解两个映像或区域是相同的, 但以下是有关如何在区域中使用副本的一般指导:

- 对于并行创建的每 20 个 VM，我们建议保留一个副本。 例如，如果要在区域中使用相同映像并行创建 120 个 VM，我们建议至少保留映像的 6 个副本。 
- 对于包含多达 600 个实例的每个规模集部署，我们建议至少保留一个副本。 例如，如果要创建 5 个规模集，而每个规模集都包含在单个区域中使用相同映像创建的 600 个 VM 实例，我们建议至少保留映像的 5 个副本。 

鉴于映像大小、内容和 OS 类型等因素，我们始终建议保留的副本数应超出该副本数。


![演示如何缩放映像的示意图](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>使映像高度可用

[Azure 区域冗余存储 (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/)可针对区域中的可用性区域故障提供复原能力。 利用共享映像库的公开上市, 你可以选择将映像存储在可用性区域区域中的 ZRS 帐户中。 

你还可以为每个目标区域选择 "帐户类型"。 默认存储帐户类型为 Standard_LRS, 但你可以为包含可用性区域的区域选择 Standard_ZRS。 在[此处](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)查看 ZRS 的区域可用性。

![显示 ZRS 的图形](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>复制
使用共享映像库还可以自动将映像复制到其他 Azure 区域。 可以根据组织的需要，将每个共享映像版本复制到不同的区域。 例如，始终在多个区域复制最新的映像，而只在 1 个区域提供所有旧版本。 这有助于节省共享映像版本的存储成本。 

创建共享映像版本后，可以更新该版本要复制到的区域。 复制到不同区域所需的时间取决于要复制的数据量，以及该版本要复制到的区域数。 在某些情况下，这可能需要几个小时。 在复制期间，可以查看每个区域的复制状态。 在一个区域中完成映像复制后，接着可以在该区域中使用该映像版本部署 VM 或规模集。

![演示如何复制映像的示意图](./media/shared-image-galleries/replication.png)


## <a name="access"></a>访问权限

由于共享映像库、映像定义和映像版本都是资源，因此，可以使用内置的本机 Azure RBAC 控件来共享这些资源。 使用 RBAC 可与其他用户、服务主体和组共享这些资源。 甚至可以与创建这些资源的租户外部的个人共享访问权限。 一旦用户有权访问共享的映像版本，他们就可以部署 VM 或虚拟机规模集。  以下共享矩阵可以帮助你了解用户有权访问哪些资源：

| 与用户共享     | 共享映像库 | 映像定义 | 映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共享映像库 | 是                  | 是          | 是                  |
| 映像定义     | 否                   | 是          | 是                  |

我们建议在库级别共享，以获得最佳体验。 我们建议不要共享单个映像版本。 有关 RBAC 的详细信息，请参阅[使用 RBAC 管理 Azure 资源的访问权限](../articles/role-based-access-control/role-assignments-portal.md)。

此外，还可以使用多租户应用注册大规模共享映像，甚至是跨租户共享。 有关在租户之间共享映像的详细信息，请参阅[在 Azure 租户之间共享库 VM 映像](../articles/virtual-machines/linux/share-images-across-tenants.md)。

## <a name="billing"></a>帐单
使用共享映像库服务不会产生额外的费用。 以下资源会产生费用：
- 存储共享映像版本的存储费用。 具体费用取决于映像版本的副本数，以及版本要复制到的区域数。 例如，如果你有 2 个映像，它们都已复制到 3 个区域，则你需要根据映像的大小，为 6 个托管磁盘付费。 有关详细信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 将第一个映像版本从源区域复制到目标区域的网络传出费用。 后续副本将在区域中处理，因此不会产生额外的费用。 

## <a name="updating-resources"></a>更新资源

创建后，可对映像库资源进行一些更改。 限制如下：
 
共享映像库：
- 描述

映像定义：
- 建议的 vCPU 数
- 建议的内存
- 描述
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域
- 从最新版本中排除
- 生命周期终结日期


## <a name="sdk-support"></a>SDK 支持

以下 SDK 支持创建共享映像库：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>模板

可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [根据映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常见问题 

**问：** 如何列出不同订阅中的所有共享映像库资源？ 
 
 A. 若要在 Azure 门户上列出不同订阅中你有权访问的所有共享映像库资源，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 转到“所有资源”。
1. 选择要列出其中的所有资源的所有订阅。
1. 查找类型为“专用库”的资源。
 
   若要查看映像定义和映像版本，还应选择“显示隐藏的类型”。
 
   若要列出不同订阅中你有权访问的所有共享映像库资源，请在 Azure CLI 中使用以下命令：

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**问：** 是否可将现有的映像移到共享映像库？
 
 A. 是的。 根据映像的类型，可能存在 3 种场景。

 方案 1：如果你有托管映像，则可以从该映像创建映像定义和映像版本。

 方案 2：如果你有非托管的通用化映像，可以从该映像创建托管映像，然后从该托管映像创建映像定义和映像版本。 

 方案 3：如果本地文件系统中包含 VHD，则需要上传 VHD、创建托管映像，然后可以从该映像创建映像定义和映像版本。
- 如果 VHD 适用于 Windows VM，请参阅[上传通用化 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
- 如果 VHD 适用于 Linux VM，请参阅[上传 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**问：** 是否可以从专用化磁盘创建映像版本？

 A. 不可以，目前不支持将专用化磁盘用作映像。 如果你有专用化磁盘，需要通过将专用化磁盘附加到新 VM，[从 VHD 创建 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk)。 运行 VM 后，需要遵照有关从 [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) 或 [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) 创建托管映像的说明操作。 创建通用化托管映像后，可以启动创建共享映像说明和映像版本的过程。

 
**问：** 创建后，是否可将共享映像库资源移到其他订阅？

 A. 不可以，无法将共享映像库资源移到其他订阅。 但是，可根据需要将库中的映像版本复制到其他区域。

**问：** 是否可以跨云（Azure 中国世纪互联、Azure 德国和 Azure 政府云）复制映像版本？ 

 A. 无法跨云复制映像版本。

**问：** 是否可以跨订阅复制映像版本？ 

 A. 不可以。但可以跨订阅中的区域复制映像版本，并通过 RBAC 在其他订阅中使用该版本。

**问：** 是否可以跨 Azure AD 租户共享映像版本？ 

 A. 是的，可以使用 RBAC 在租户之间对个人共享。 但是，若要大规模共享，请参阅如何使用 [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) 或 [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)“在 Azure 租户之间共享库映像”。


**问：** 跨目标区域复制映像版本需要多长时间？

 A. 映像版本复制时间完全取决于映像的大小，以及它要复制到的区域数。 但是，作为最佳做法，我们建议缩小映像，并在相互靠近的源与目标区域之间进行复制，以获得最佳效果。 可以使用 -ReplicationStatus 标志检查复制状态。


**问：** 源区域与目标区域之间的区别是什么？

 A. 源区域是创建映像版本的区域，而目标区域是存储映像版本副本的区域。 每个映像版本只能有一个源区域。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。  


**问：** 创建映像版本时如何指定源区域？

 A. 创建映像版本时，可以在 CLI 中使用 **-location** 标记或者在 PowerShell 中使用 **-Location** 标记，来指定源区域。 请确保要用作基本映像来创建映像版本的托管映像，位于创建映像版本的同一位置。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。  


**问：** 如何指定要在每个区域中创建的映像版本副本数？

 A. 可通过两种方式指定要在每个区域中创建的映像版本副本数：
 
1. 区域副本计数：指定要在每个区域创建的副本数。 
2. 通用副本计数：未指定区域副本计数时每个区域的默认计数。 

若要指定区域副本计数，请传递位置以及要在该区域中创建的副本数：“美国中南部=2”。 

如果未为每个位置指定区域副本计数，则默认副本数将是指定的通用副本计数。 

若要在 CLI 中指定通用副本计数，请在 `az sig image-version create` 命令中使用 **--replica-count** 参数。


**问：** 是否可以不在创建映像定义和映像版本的位置创建共享映像库？

 A. 是的，可以这样做。 但作为最佳做法，我们建议将资源组、共享映像库、映像定义和映像版本保留在同一位置。


**问：** 使用共享映像库会产生哪些费用？

 A. 使用共享映像库服务不会产生费用，不过，存储映像版本会产生存储费用，将映像版本从源区域复制到目标区域会产生网络传出费用。

**问：** 应使用哪个 API 版本来创建共享映像库、映像定义和映像版本，并基于映像版本创建 VM/VMSS？

 A. 若要使用映像版本部署 VM 和虚拟机规模集，我们建议使用 API 2018-04-01 或更高版本。 若要处理共享映像库、映像定义和映像版本，我们建议使用 API 版本 2018-06-01。 区域冗余存储 (ZRS) 需要 2019-03-01 版或更高版本。
