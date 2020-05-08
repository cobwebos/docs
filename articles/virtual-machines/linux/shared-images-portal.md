---
title: 使用门户创建共享的 Azure Linux VM 映像
description: 了解如何使用 Azure 门户创建和共享 Linux 虚拟机映像。
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 28cdc96020d085c6f44c8b6818aa76dd7eb29891
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788973"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>使用门户创建 Azure 共享映像库

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完全基于角色的访问控制 (RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

共享映像库功能具有多种资源类型。 我们将在本文中使用或生成这些资源类型：


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>开始之前

若要完成本文中的示例，必须准备好通用化 VM 的现有托管映像，或专用化 VM 的快照。 可以按照[教程：使用 Azure PowerShell 创建 AZURE VM 的自定义映像](tutorial-custom-images.md)，创建托管映像，或创建专用 VM 的[快照](../windows/snapshot-copy-managed-disk.md)。 对于托管映像和快照，数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>创建 VM 

现在，可以创建一个或多个新的 VM。 本示例在美国东部数据中心的“myResourceGroup”中创建名为“myVMfromImage”的 VM******。

1. 转到映像定义。 可以使用资源筛选器显示所有可用的映像定义。
1. 在映像定义的页面顶部，从菜单中选择“创建 VM”。****
1. 对于“资源组”，请选择“新建”并键入 *myResourceGroup* 作为名称。********
1. 在“虚拟机名称”中键入 *myVM*。****
1. 对于 "**区域**"，选择 "*美国东部*"。
1. 对于“可用性选项”，请保留默认设置“无需基础结构冗余”。******
1. 如果你是从映像定义的页面开始操作的，系统会自动使用 `latest` 映像版本填充“映像”的值。****
1. 对于“大小”，请从可用大小列表中选择一种 VM 大小，然后选择“选择”。********
1. 在“管理员帐户”下，如果源 VM 是通用化的，请输入**用户名**和 **SSH 公钥**。**** 如果源 VM 是专用化的，则这些选项将会灰显，因为系统会使用源 VM 中的信息。
1. 若要允许远程访问 VM，请在“公共入站端口”下选择“允许所选端口”，然后从下拉列表中选择“SSH (22)”。************ 如果你不希望允许远程访问 VM，请为“公共入站端口”保留选择“无”。********
1. 完成后，选择页面底部的“查看 + 创建”按钮。****
1. VM 通过验证后，选择页面底部的“创建”以开始部署。****


## <a name="clean-up-resources"></a>清理资源

当不再需要时，可以删除资源组、虚拟机和所有相关资源。 为此，请选择虚拟机的资源组，选择“删除”  ，然后确认要删除的资源组的名称。

若要删除单个资源，需要按相反的顺序删除。 例如，若要删除某个映像定义，需要先删除基于该映像创建的所有映像版本。

## <a name="next-steps"></a>后续步骤

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [从映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

有关共享映像库的详细信息，请参阅[概述](shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](troubleshooting-shared-images.md)。

