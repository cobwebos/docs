---
title: 使用门户创建适用于 Windows 的共享 Azure 虚拟机映像
description: 了解如何使用 Azure 门户来创建和共享虚拟机映像。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 0823942964f2bdb9f943d6eb778a3132574af0f7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065530"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>使用 Azure 门户创建共享映像库

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完全基于角色的访问控制 (RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

共享映像库功能具有多种资源类型。 我们将在本文中使用或生成这些资源类型：

| 资源 | 说明|
|----------|------------|
| **托管映像** | 一个基本映像，可以单独使用，也可用于在映像库中创建**映像版本**。 托管映像是从[通用化](shared-image-galleries.md#generalized-and-specialized-images)vm 创建的。 托管映像是一种特殊的 VHD 类型，可用于生成多个 VM，并且现在可用于创建共享映像版本。 |
| **快照** | 可用于生成**映像版本**的 VHD 副本。 快照可以从[专用](shared-image-galleries.md#generalized-and-specialized-images)VM （一个尚未通用化的虚拟机）中获取，然后单独使用或与数据磁盘的快照一起使用，以创建专用的映像版本。
| **映像库** | 与 Azure 市场一样，**映像库**是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像在库中定义，携带有关该映像及其在组织内部使用的要求的信息。 您可以包括诸如映像是通用化还是专用的信息、操作系统、最小和最大内存要求以及发行说明。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用**映像版本**来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用**映像版本**创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |

<br>


> [!IMPORTANT]
> 专用图像目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知预览版限制**只能使用门户或 API 从专用映像创建 Vm。 对于预览，无 CLI 或 PowerShell 支持。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的示例，您必须具有通用化 VM 的现有托管映像或专用 VM 的快照。 可以按照[教程：使用 Azure PowerShell 创建 AZURE VM 的自定义映像](tutorial-custom-images.md)，创建托管映像，或创建专用 VM 的[快照](snapshot-copy-managed-disk.md)。 对于托管映像和快照，数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>创建 VM

现在，你可以创建一个或多个新 Vm。 此示例在*美国东部*数据中心的*myResourceGroup*中创建名为*myVM*的 VM。

1. 请参阅映像定义。 可以使用资源筛选器显示所有可用的映像定义。
1. 在映像定义的页面上，从页面顶部的菜单中选择 "**创建 VM** "。
1. 对于 "**资源组**"，选择 "**新建**"，然后键入*myResourceGroup*作为名称。
1. 在 "**虚拟机名称**" 中，键入*myVM*。
1. 对于“区域”，请选择“美国东部”。
1. 对于**可用性选项**，保留默认值 "*无需基础结构冗余*"。
1. 如果从图像定义的页面开始，则会用 `latest` 映像版本自动填充**图像**的值。
1. 对于 "**大小**"，请从可用大小列表中选择 VM 大小，然后选择 "**选择**"。
1. 在 "**管理员帐户**" 下，如果映像已通用化，则需要提供用户名，如*azureuser*和密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。 如果映像是专用的，"用户名" 和 "密码" 字段将灰显，因为使用了源 VM 的用户名和密码。
1. 如果要允许远程访问 VM，请在 "**公用入站端口**" 下，选择 "**允许选择的端口**"，然后从下拉选项中选择 " **RDP （3389）** "。 如果你不希望允许远程访问 VM，请为**公用入站端口**保留 "**无**"。
1. 完成后，请选择页面底部的 "查看" 和 "**创建**" 按钮。
1. VM 通过验证后，在页面底部选择 "**创建**" 以开始部署。


## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

如果要删除单个资源，需要按相反的顺序删除它们。 例如，若要删除映像定义，需要删除所有从该映像创建的映像版本。

## <a name="next-steps"></a>后续步骤

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [根据映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

有关共享映像库的详细信息，请参阅[概述](shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](troubleshooting-shared-images.md)。

