---
title: include 文件
description: include 文件
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 2d0030549acdb55ce2be94534ec59bb07b11869d
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221624"
---
共享映像库是一种服务，可帮助你围绕映像构建结构和组织。 共享映像库提供：

- 支持映像全局复制。
- 对映像进行版本控制和分组，以便于管理。
- 在支持可用性区域的区域中，具有区域冗余存储 (ZRS) 帐户的高可用性映像。 ZRS 提高了针对区域性故障的恢复能力。
- 高级存储支持 (Premium_LRS)。
- 使用 RBAC 在订阅之间，甚至在 Active Directory (AD) 租户之间共享。
- 使用每个区域中的映像副本缩放部署。

使用共享映像库，可以将映像共享给组织内的不同用户、服务主体或 AD 组。 共享映像可以复制到多个区域，以便更快地扩展部署。

映像是完整 VM（包括任何附加的数据磁盘）的副本或者只是 OS 磁盘的副本，具体取决于映像的创建方式。 从映像创建 VM 时，将使用该映像中的 VHD 副本来为新 VM 创建磁盘。 映像保留在存储中，可反复用来创建新的 VM。

如果你有大量的映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库。 

共享映像库功能具有多种资源类型：

| 资源 | 说明|
|----------|------------|
| **映像源** | 这是可用于在映像库中创建“映像版本”的资源。 映像源可以是[通用或专用](#generalized-and-specialized-images)的现有 Azure VM，也可以是托管映像、快照、VHD 或其他映像库中的映像版本。 |
| **映像库** | 与 Azure 市场一样，**映像库**是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像定义在库中创建，携带有关该映像以及在内部使用该映像的要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用**映像版本**来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用**映像版本**创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |

<br>

![演示如何在库中创建多个映像版本的示意图](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>映像定义

映像定义是映像版本的逻辑分组。 映像定义包含的信息涉及创建映像的原因、映像适用的 OS，以及映像的用法。 映像定义就像围绕创建特定映像计划所有详细信息。 不要从映像定义部署 VM，而要从基于该定义创建的映像版本部署 VM。

每个映像定义有三个可以组合使用的参数：发布者、产品/服务和 SKU  。 这些参数用于查找特定的映像定义。 可以拥有共享一个或两个但不是全部三个值的映像版本。  例如，以下是三个映像定义及其值：

|映像定义|发布者|产品/服务|SKU|
|---|---|---|---|
|myImage1|Contoso|Finance|后端|
|myImage2|Contoso|财务|前端|
|myImage3|测试|财务|前端|

所有这三个映像都有唯一的一组值。 格式类似于当前在 Azure PowerShell 中为 [Azure 市场映像](../articles/virtual-machines/windows/cli-ps-findimage.md)指定发布者、套餐和 SKU，以获取最新市场映像版本的方式。 每个映像定义需要包含一组唯一的这些值。

下面是可在映像定义上设置的其他参数，以便你可以更轻松地跟踪资源：

* 操作系统状态 - 可将 OS 状态设置为[通用化或专用化](#generalized-and-specialized-images)。
* 操作系统 - 可以是 Windows 或 Linux。
* 说明 - 使用说明可以更详细地解释该映像定义为何存在。 例如，可为预装了应用程序的前端服务器创建一个映像定义。
* Eula - 可用于指向特定于映像定义的最终用户许可协议。
* 隐私声明和发行说明 - 将发行说明和隐私声明存储在 Azure 存储中，并提供在映像定义中用于访问它们的 URI。
* 生命周期结束日期 - 将生命周期结束日期附加到映像定义，以便能够使用自动化功能删除旧的映像定义。
* 标记 - 可以在创建映像定义时添加标记。 有关标记的详细信息，请参阅[使用标记来组织资源](../articles/azure-resource-manager/management/tag-resources.md)。
* 最小和最大 vCPU 与内存建议量 - 如果映像附带 vCPU 和内存建议量，则你可以将该信息附加到映像定义。
* 不允许的磁盘类型 - 可以提供有关 VM 所需存储的信息。 例如，如果映像不适合标准 HDD 磁盘，请将其添加到禁止列表。
* Hyper-V 生成 - 可以指定是从第 1 代还是从第 2 代 Hyper-V VHD 创建映像。
* Marketplace 映像的购买计划信息： `-PurchasePlanPublisher ` 、 `-PurchasePlanName` 和 `-PurchasePlanProduct` 。 有关购买计划信息的详细信息，请参阅[在 Azure marketplace 中查找映像](https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage)和[在创建映像时提供 azure Marketplace 购买计划信息](../articles/virtual-machines/marketplace-images.md)。

## <a name="generalized-and-specialized-images"></a>通用和专用映像

共享映像库支持两种操作系统状态。 通常，映像要求在创建映像之前，用于创建映像的 VM 已通用化。 通用化是从 VM 中删除计算机和用户特定信息的过程。 对于 Windows，使用 Sysprep 工具。 对于 Linux，可以使用 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 或 `-deprovision+user` 参数。

专用化 VM 尚未经历删除计算机特定信息和帐户的过程。 此外，使用专用映像创建的 VM 不具有与其关联的 `osProfile`。 这意味着，除了某些优点外，专用化映像还存在一些限制。

- 从专用映像创建的 VM 和规模集可以更快地启动并运行。 由于它们是从已经开始启动的源创建的，因此从这些映像创建的 VM 启动速度会更快。
- 可用于登录到 VM 的帐户也可用于通过使用该 VM 创建的专用映像创建的任何 VM。
- VM 具有用于创建映像的 VM 的**计算机名**。 应更改计算机名以避免冲突。
- `osProfile` 是使用 `secrets` 将某些敏感信息传递给 VM 的方式。 在使用 KeyVault、WinRM，以及在 `osProfile` 中使用 `secrets` 的其他功能时，这可能会导致出现问题。 在某些情况下，可以使用托管服务标识 (MSI) 解决这些限制。

## <a name="regional-support"></a>区域支持

所有公共区域都可以是目标区域，要复制到澳大利亚中部和澳大利亚中部 2，需要将订阅列入允许列表。 要请求允许列表，请转到： https://azure.microsoft.com/global-infrastructure/australia/contact/

## <a name="limits"></a>限制 

使用共享映像库部署资源时，每个订阅存在限制：
- 每个区域的每个订阅限制为 100 个共享映像库
- 每个区域的每个订阅限制为 1,000 个映像定义
- 每个区域每个订阅限 10,000 个映像版本
- 每个区域的每个订阅限制为 10 个映像版本副本
- 附加到映像的任何磁盘的大小必须小于或等于 1 TB

有关详细信息，请参阅[根据限制检查资源使用情况](https://docs.microsoft.com/azure/networking/check-usage-against-limits)，以获取有关如何检查当前使用情况的示例。
 
## <a name="scaling"></a>扩展
使用共享映像库可以指定要让 Azure 保留的映像副本数。 这有助于实现多 VM 部署方案，因为可将 VM 部署分散到不同的副本，减少单个副本过载导致实例创建过程受到限制的可能性。

现在，使用共享映像库，最多可在虚拟机规模集中部署 1,000 个 VM 实例（相比使用托管映像部署 600 个有所增加）。 映像副本可用于提高部署性能、可靠性和一致性。  可以在每个目标区域中设置不同的副本计数，具体视该区域的缩放需求而定。 由于每个副本是映像的深层复制，因此，这有助于使用每个额外的副本线性地缩放部署。 我们知道了没有两个图像或区域是相同的，不过以下提供了有关如何在区域中使用副本的一般准则：

- 对于非虚拟机规模集 (VMSS) 部署 - 对于同时创建的每 20 个 VM，我们建议保留一个副本。 例如，如果使用某个区域中的同一映像同时创建 120 个 VM，我们建议你保留至少 6 个映像副本。 
- 对于虚拟机规模集 (VMSS) 部署 - 对于包含多达 600 个实例的每个规模集部署，我们建议至少保留一个副本。 例如，如果同时创建 5 个规模集，每个规模集具有 600 个使用某个区域中的同一映像的 VM 实例，则建议至少保留 5 个映像副本。 

鉴于映像大小、内容和 OS 类型等因素，我们始终建议保留的副本数应超出该副本数。

![演示如何缩放映像的示意图](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>使映像具有高可用性

[Azure 区域冗余存储 (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) 提供了针对区域中可用性区域故障的恢复能力。 随着共享映像库的正式发布，你可以选择使用可用性区域将映像存储在 ZRS 帐户中。 

你还可以为每个目标区域选择帐户类型。 默认存储帐户类型为 Standard_LRS，但你可以使用可用性区域选择各区域的 Standard_ZRS。 在[此处](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)查看 ZRS 的区域可用性。

![显示 ZRS 的图形](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>复制
使用共享映像库还可以自动将映像复制到其他 Azure 区域。 可以根据组织的需要，将每个共享映像版本复制到不同的区域。 例如，始终在多个区域复制最新的映像，而只在 1 个区域提供所有旧版本。 这有助于节省共享映像版本的存储成本。 

创建共享映像版本后，可以更新该版本要复制到的区域。 复制到不同区域所需的时间取决于要复制的数据量，以及该版本要复制到的区域数。 在某些情况下，这可能需要几个小时。 在复制期间，可以查看每个区域的复制状态。 在一个区域中完成映像复制后，接着可以在该区域中使用该映像版本部署 VM 或规模集。

![演示如何复制映像的示意图](./media/shared-image-galleries/replication.png)

## <a name="access"></a>访问

由于共享映像库、映像定义和映像版本都是资源，因此，可以使用内置的本机 Azure RBAC 控件来共享这些资源。 使用 RBAC 可与其他用户、服务主体和组共享这些资源。 甚至可以与创建这些资源的租户外部的个人共享访问权限。 一旦用户有权访问共享的映像版本，他们就可以部署 VM 或虚拟机规模集。  以下共享矩阵可以帮助你了解用户有权访问哪些资源：

| 与用户共享     | 共享的映像库 | 映像定义 | 映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共享的映像库 | 是                  | 适合          | 适合                  |
| 映像定义     | 否                   | 适合          | 是                  |

建议在库级别共享以获得最佳体验。 我们建议不要共享单个映像版本。 有关 RBAC 的详细信息，请参阅[使用 RBAC 管理对 Azure 资源的访问权限](../articles/role-based-access-control/role-assignments-portal.md)。

此外，还可以使用多租户应用注册大规模共享映像，甚至是跨租户共享。 有关跨租户共享映像的详细信息，请参阅[在 Azure 租户之间共享库 VM 映像](../articles/virtual-machines/linux/share-images-across-tenants.md)。

## <a name="billing"></a>计费
使用共享映像库服务不会产生额外的费用。 以下资源会产生费用：
- 存储共享映像版本的存储费用。 费用取决于映像版本的副本数，以及版本要复制到的区域数。 例如，如果你有 2 个映像，并将其同时复制到 3 个区域，则你需要根据映像的大小，为 6 个托管磁盘付费。 有关详细信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。
- 将第一个映像版本从源区域复制到目标区域的网络传出费用。 后续副本将在区域中处理，因此不会产生额外的费用。 

## <a name="updating-resources"></a>更新资源

创建后，可对映像库资源进行一些更改。 限制如下：
 
共享映像库：
- 描述

映像定义：
- 建议的 vCPU 数
- 建议的内存
- 说明
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域数
- 从最新版本中排除
- 生命周期终结日期

## <a name="sdk-support"></a>SDK 支持

以下 SDK 支持创建共享映像库：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>模板

可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [根据映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常见问题 

* [如何列出不同订阅中的所有共享映像库资源？](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [是否可将现有的映像移到共享映像库？](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [是否可以从专用磁盘创建映像版本？](#can-i-create-an-image-version-from-a-specialized-disk)
* [创建共享映像库资源后，是否可以将其移动到其他订阅中？](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [是否可以跨云（例如，Azure 中国世纪互联、Azure 德国云或 Azure 政府云）复制映像版本？](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [是否可以跨订阅复制映像版本？](#can-i-replicate-my-image-versions-across-subscriptions)
* [是否可以跨 Azure AD 租户共享映像版本？](#can-i-share-image-versions-across-azure-ad-tenants)
* [跨目标区域复制映像版本需要多长时间？](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [源区域与目标区域之间的区别是什么？](#what-is-the-difference-between-source-region-and-target-region)
* [创建映像版本时如何指定源区域？](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [如何指定要在每个区域中创建的映像版本副本数？](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [是否可以不在创建映像定义和映像版本的位置创建共享映像库？](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [使用共享映像库会产生哪些费用？](#what-are-the-charges-for-using-the-shared-image-gallery)
* [应使用哪个 API 版本来创建共享映像库、映像定义和映像版本？](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [我应该使用哪种 API 版本来通过映像版本创建共享 VM 或虚拟机规模集？](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [能否将使用托管映像创建的虚拟机规模集更新为使用共享映像库映像？]

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>如何列出不同订阅中的所有共享映像库资源？

若要在 Azure 门户上列出不同订阅中你有权访问的所有共享映像库资源，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 向下滚动页面并选择“所有资源”。
1. 选择要列出其中的所有资源的所有订阅。
1. 查找类型为“共享映像库”的资源。
  
若要列出不同订阅中你有权访问的所有共享映像库资源，请在 Azure CLI 中使用以下命令：

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

有关详细信息，请参阅使用 [Azure CLI](../articles/virtual-machines/update-image-resources-cli.md) 或 [PowerShell](../articles/virtual-machines/update-image-resources-powershell.md) 管理库资源。

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>是否可将现有的映像移到共享映像库？
 
是的。 根据映像的类型，可能存在 3 种场景。

 应用场景 1：如果你有托管映像，则可以从该映像创建映像定义和映像版本。 有关详细信息，请参阅使用 [Azure CLI](../articles/virtual-machines/image-version-managed-image-cli.md) 或 [PowerShell](../articles/virtual-machines/image-version-managed-image-powershell.md) 从托管映像迁移到映像版本。

 应用场景 2：如果你有非托管的映像，可以从该映像创建托管映像，然后从该托管映像创建映像定义和映像版本。 

 应用场景 3：如果本地文件系统中包含 VHD，则需要将 VHD 上传到托管映像，然后可以从该映像创建映像定义和映像版本。

- 如果 VHD 适用于 Windows VM，请参阅[上传 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
- 如果 VHD 适用于 Linux VM，请参阅[上传 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>是否可以从专用化磁盘创建映像版本？

是，可以使用[CLI](../articles/virtual-machines/vm-specialized-image-version-cli.md)、 [PowerShell](../articles/virtual-machines/vm-specialized-image-version-powershell.md)或 API 从专用映像创建 VM。 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>创建共享映像库资源后，是否可以将其移动到其他订阅中？

不可以，无法将共享映像库资源移到其他订阅。 可以将库中的映像版本复制到其他区域，也可以使用 [Azure CLI](../articles/virtual-machines/image-version-another-gallery-cli.md) 或 [PowerShell](../articles/virtual-machines/image-version-another-gallery-powershell.md) 从其他库复制映像。

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>是否可以跨云（例如 Azure 中国世纪互联、Azure 德国或 Azure 政府云）复制映像版本？

无法跨云复制映像版本。

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>是否可以跨订阅复制映像版本？

不可以。但可以跨订阅中的区域复制映像版本，并通过 RBAC 在其他订阅中使用该版本。

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>是否可以跨 Azure AD 租户共享映像版本？ 

是的，可以使用 RBAC 跨租户共享给个人。 但若要大规模共享，请参阅使用 [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) 或 [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)“跨 Azure 租户共享库映像”。

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>跨目标区域复制映像版本需要多长时间？

映像版本复制时间完全取决于映像的大小，以及它要复制到的区域数。 但是，作为最佳做法，我们建议缩小映像，并在相互靠近的源与目标区域之间进行复制，以获得最佳效果。 可以使用 -ReplicationStatus 标志检查复制状态。

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>源区域与目标区域之间的区别是什么？

源区域是创建映像版本的区域，而目标区域是存储映像版本副本的区域。 每个映像版本只能有一个源区域。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>创建映像版本时如何指定源区域？

创建映像版本时，可以在 CLI 中使用 **-location** 标记或者在 PowerShell 中使用 **-Location** 标记，来指定源区域。 请确保要用作基本映像来创建映像版本的托管映像，位于创建映像版本的同一位置。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>如何指定要在每个区域中创建的映像版本副本数？

可通过两种方式指定要在每个区域中创建的映像版本副本数：
 
1. 区域副本计数：指定要在每个区域创建的副本数。 
2. 通用副本计数：未指定区域副本计数时每个区域的默认计数。 

若要指定区域副本计数，请传递位置以及要在该区域中创建的副本数：“美国中南部=2”。 

如果未为每个位置指定区域副本计数，则默认副本数将是指定的通用副本计数。 

若要在 CLI 中指定通用副本计数，请在 `az sig image-version create` 命令中使用 **--replica-count** 参数。

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>是否可以不在创建映像定义和映像版本的位置创建共享映像库？

是，可以这样做。 但作为最佳做法，我们建议将资源组、共享映像库、映像定义和映像版本保留在同一位置。

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>使用共享映像库会产生哪些费用？

使用共享映像库服务不会产生费用，不过，存储映像版本会产生存储费用，将映像版本从源区域复制到目标区域会产生网络传出费用。

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>应使用哪个 API 版本来创建共享映像库、映像定义和映像版本？

若要处理共享映像库、映像定义和映像版本，我们建议使用 API 版本 2018-06-01。 区域冗余存储 (ZRS) 需要版本 2019-03-01 或更高版本。

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>我应该使用哪种 API 版本来通过映像版本创建共享 VM 或虚拟机规模集？

若要使用映像版本部署 VM 和虚拟机规模集，我们建议使用 API 2018-04-01 或更高版本。

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>是否可以将使用托管映像创建的虚拟机规模集更新为使用共享映像库映像？

是的，你可以将规模集映像引用从托管映像更新为共享映像库映像，前提是这些映像之间的 OS 类型、Hyper-V 生成和数据磁盘布局均匹配。 
