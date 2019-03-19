---
title: include 文件
description: include 文件
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8c7da8d04b456642b158dda77d9c745891aa18e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58051868"
---
共享映像库是可以帮助你围绕自定义托管 VM 映像生成结构和组织的服务。 使用共享映像库，可以将映像共享给组织内的不同用户、服务主体或 AD 组。 共享映像可以复制到多个区域，以便更快地扩展部署。

托管映像是完整 VM（包括任何附加的数据磁盘）的副本或者只是 OS 磁盘的副本，具体取决于映像的创建方式。 从映像创建 VM 时，将使用该映像中的 VHD 副本来为新 VM 创建磁盘。 托管映像保留在存储中，可反复用来创建新的 VM。

如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地更新和共享映像。 使用共享映像库产生的费用只是映像所用的存储的费用，加上将映像从源区域复制到发布区域所产生的网络传出费用。

共享映像库功能具有多种资源类型：

| 资源 | 描述|
|----------|------------|
| **托管映像** | 这是基本映像，可以单独使用，也可用于在映像库中创建“映像版本”。 托管映像是从通用 VM 创建的。 托管映像是一种特殊的 VHD 类型，可用于生成多个 VM，并且现在可用于创建共享映像版本。 |
| **映像库** | 与 Azure 市场一样，**映像库**是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像在库中定义，携带有关该映像及其在内部使用的要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用**映像版本**来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用**映像版本**创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |

<br>


![演示如何在库中创建多个映像版本的示意图](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>区域支持

共享映像库的区域支持以受限预览版提供，但会不断扩大。 下面针对受限预览版列出了可以创建库的区域列表，以及可以复制任何库映像的区域列表： 

| 创建库的位置  | 将版本复制到 |
|--------------------|----------------------|
| 美国中西部    |所有公共区域 &#42;|
| 美国东部 2          ||
| 美国中南部   ||
| 东南亚     ||
| 西欧        ||
| 美国西部            ||
| 美国东部            ||
| 加拿大中部     ||
|                    ||



&#42; 要复制到澳大利亚中部和澳大利亚中部 2，需要将订阅列入允许列表。 要请求允许列表，请转到： https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>扩展
使用共享映像库可以指定要让 Azure 保留的映像副本数。 这有助于实现多 VM 部署方案，因为可将 VM 部署分散到不同的副本，减少单个副本过载导致实例创建过程受到限制的可能性。

![演示如何缩放映像的示意图](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>复制
使用共享映像库还可以自动将映像复制到其他 Azure 区域。 可以根据组织的需要，将每个共享映像版本复制到不同的区域。 例如，始终在多个区域复制最新的映像，而只在 1 个区域提供所有旧版本。 这有助于节省共享映像版本的存储成本。 

创建共享映像版本后，可以更新该版本要复制到的区域。 复制到不同区域所需的时间取决于要复制的数据量，以及该版本要复制到的区域数。 在某些情况下，这可能需要几个小时。 在复制期间，可以查看每个区域的复制状态。 在一个区域中完成映像复制后，接着可以在该区域中使用该映像版本部署 VM 或 VMSS。

![演示如何复制映像的示意图](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
与共享映像库一样，共享映像和共享映像版本都是资源，可以使用内置的本机 Azure RBAC 控件来共享它们。 使用 RBAC 可与组织中的其他用户、服务主体和组共享这些资源。 这些资源的共享范围为同一个 Azure AD 租户。 用户获取共享映像版本的访问权限后，可以在该共享映像版本所在的同一 Azure AD 租户内他们有权访问的任何订阅中部署 VM 或虚拟机规模集。  以下共享矩阵可以帮助你了解用户有权访问哪些资源：

| 与用户共享     | 共享的映像库 | 共享映像 | 共享映像版本 |
|----------------------|----------------------|--------------|----------------------|
| 共享的映像库 | 是                  | 是          | 是                  |
| 共享映像         | 否                   | 是          | 是                  |
| 共享映像版本 | 否                   | 否           | 是                  |



## <a name="billing"></a>计费
使用共享映像库服务不会产生额外的费用。 以下资源会产生费用：
- 存储共享映像版本的存储费用。 这取决于版本的副本数，以及版本要复制到的区域数。
- 从版本源区域复制到目标区域的网络传出费用。

## <a name="sdk-support"></a>SDK 支持

以下 SDK 支持创建共享映像库：

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>模板

可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [根据映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>常见问题 

**问：** 如何注册共享映像库公共预览版？
 
 A. 若要注册共享映像库公共预览版，需要在你打算在其中创建共享映像库、映像定义或映像版本资源，以及打算在其中使用映像版本部署虚拟机的每个订阅中运行以下命令来注册该功能。

**CLI**： 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

PowerShell： 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**问：** 如何在订阅之间共享映像？
 
 A. 可以使用基于角色的访问控制 (RBAC) 在订阅之间共享映像。 任何对映像版本具有读取权限的用户，即使跨订阅，也能够使用映像版本部署虚拟机。


**问：** 是否可将现有的映像移到共享映像库？
 
 A. 是的。 根据映像的类型，可能存在 3 种场景。

 方案 1：如果你有托管映像，则可以从该映像创建映像定义和映像版本。

 方案 2：如果你有非托管的通用化映像，可以从该映像创建托管映像，然后从该托管映像创建映像定义和映像版本。 

 方案 3：如果本地文件系统中包含 VHD，则需要上传 VHD、创建托管映像，然后可以从该映像创建映像定义和映像版本。
- 如果 VHD 适用于 Windows VM，请参阅[上传通用化 VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)。
- 如果 VHD 适用于 Linux VM，请参阅[上传 VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**问：** 是否可以从专用化磁盘创建映像版本？

 A. 不可以，目前不支持将专用化磁盘用作映像。 如果你有专用化磁盘，需要通过将专用化磁盘附加到新 VM，[从 VHD 创建 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk)。 运行 VM 后，需要遵照有关从 [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) 或 [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) 创建托管映像的说明操作。 创建通用化托管映像后，可以启动创建共享映像说明和映像版本的过程。


**问：** 是否可以通过 Azure 门户创建共享映像库、映像定义和映像版本？

 A. 不可以，目前不支持通过 Azure 门户创建任何共享映像库资源。 但是，我们确实支持通过 CLI、模板和 SDK 创建共享映像库资源。 对 PowerShell 的支持即将发布。

 
**问：** 创建后，是否可以更新映像定义或映像版本？ 可以修改哪种类型的详细信息？

 A. 下面提到了可在每个资源中更新的详细信息：
 
共享映像库：
- 描述

映像定义：
- 建议的 vCPU 数
- 内存
- 描述
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域数
- 从最新项中排除
- 生命周期终结日期


**问：** 创建后，是否可将共享映像库资源移到其他订阅？

 A. 不可以，无法将共享映像库资源移到其他订阅。 但是，可根据需要将库中的映像版本复制到其他区域。

**问：** 是否可以跨云（Azure 中国世纪互联、Azure 德国和 Azure 政府云）复制映像版本？ 

 A. 无法跨云复制映像版本。

**问：** 是否可以跨订阅复制映像版本？ 

 A. 不可以。但可以跨订阅中的区域复制映像版本，并通过 RBAC 在其他订阅中使用该版本。

**问：** 是否可以跨 Azure AD 租户共享映像版本？ 

 A. 不可以，目前共享映像库不支持跨 Azure AD 租户共享映像版本。 但是，可以使用 Azure 市场中的“专有产品/服务”功能来实现此目的。


**问：** 跨目标区域复制映像版本需要多长时间？

 A. 映像版本复制时间完全取决于映像的大小，以及它要复制到的区域数。 但是，作为最佳做法，我们建议缩小映像，并在相互靠近的源与目标区域之间进行复制，以获得最佳效果。 可以使用 -ReplicationStatus 标志检查复制状态。


**问：** 在一个订阅中可以创建多少个共享映像库？

 A. 默认配额是： 
- 每个区域每个订阅为 10 个共享映像库
- 每个区域每个订阅为 200 个映像定义
- 每个区域每个订阅为 2000 个映像版本


**问：** 源区域与目标区域之间的区别是什么？

 A. 源区域是创建映像版本的区域，而目标区域是存储映像版本副本的区域。 每个映像版本只能有一个源区域。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。  


**问：** 创建映像版本时如何指定源区域？

 A. 创建映像版本时，可以在 CLI 中使用 **-location** 标记或者在 PowerShell 中使用 **-Location** 标记，来指定源区域。 请确保要用作基本映像来创建映像版本的托管映像，位于创建映像版本的同一位置。 此外，在创建映像版本时，请确保将源区域位置传递为目标区域之一。  


**问：** 如何指定要在每个区域中创建的映像版本副本数？

 A. 可通过两种方式指定要在每个区域中创建的映像版本副本数：
 
1. 区域副本计数：指定要在每个区域创建的副本数。 
2. 通用副本计数：未指定区域副本计数时每个区域的默认计数。 

若要指定区域副本计数，请传递位置以及要在该区域中创建的副本数，例如：“美国中南部=2”。 

如果未为每个位置指定区域副本计数，则默认副本数将是指定的通用副本计数。 

若要在 CLI 中指定通用副本计数，请在 `az sig image-version create` 命令中使用 **--replica-count** 参数。


**问：** 是否可以不在创建映像定义和映像版本的位置创建共享映像库？

 A. 是的，这是可行的。 但作为最佳做法，我们建议将资源组、共享映像库、映像定义和映像版本保留在同一位置。


**问：** 使用共享映像库会产生哪些费用？

 A. 使用共享映像库服务不会产生费用，不过，存储映像版本会产生存储费用，将映像版本从源区域复制到目标区域会产生网络传出费用。

**问：** 应使用哪个 API 版本来创建共享映像库、映像定义和映像版本，并基于映像版本创建 VM/VMSS？

 A. 若要使用映像版本部署 VM 和虚拟机规模集，我们建议使用 API 2018-04-01 或更高版本。 若要处理共享映像库、映像定义和映像版本，我们建议使用 API 版本 2018-06-01。 
