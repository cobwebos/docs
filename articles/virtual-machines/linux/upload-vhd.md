---
title: 使用 Azure CLI 上传或复制自定义 Linux VM
description: 使用资源管理器部署模型和 Azure CLI 上传或复制自定义的虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 70fff041cd693a19269b11398947fb0c8ce56bb1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385032"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>使用 Azure CLI 从自定义磁盘创建 Linux VM

<!-- rename to create-vm-specialized -->

本文介绍如何上传自定义虚拟硬盘 (VHD)，以及如何在 Azure 中复制现有的 VHD。 然后，使用新建的 VHD 来创建新的 Linux 虚拟机 (VM)。 可根据要求安装并配置 Linux 分发版，然后使用该 VHD 创建新的 Azure 虚拟机。

若要从自定义磁盘创建多个 VM，请先从 VM 或 VHD 创建映像。 有关详细信息，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)。

可使用两个选项创建自定义磁盘：
* 上传 VHD
* 复制现有的 Azure VM


## <a name="requirements"></a>要求
若要完成以下步骤，需要：

- 已准备好在 Azure 中使用的 Linux 虚拟机。 本文的[准备 VM](#prepare-the-vm) 部分介绍了如何查找有关安装 Azure Linux 代理 (waagent) 的特定于分发版的信息，通过 SSH 连接到 VM 时需要用到该代理。
- 用于将 [Azure 认可的 Linux 分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（或参阅[关于未认可分发版的信息](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）安装到采用 VHD 格式的虚拟磁盘的 VHD 文件。 可使用多种工具创建 VM 和 VHD：
  - 安装并配置 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](https://www.linux-kvm.org/page/RunningKVM)，并注意使用 VHD 作为映像格式。 如果需要，可以使用 `qemu-img convert`[转换映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
  - 也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支持更新的 VHDX 格式。 创建 VM 时，请将 VHD 指定为映像格式。 如果需要，可以使用 [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet 将 VHDX 磁盘转换为 VHD。 Azure 不支持上传动态 VHD，因此，上传之前，你需要将此类磁盘转换为静态 VHD。 可以使用 [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) 等工具在将动态磁盘上传到 Azure 的过程中转换磁盘。
> 
> 


- 确保已安装了最新的 [Azure CLI](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure 帐户。

在下面的示例中，请将示例参数名称替换为自己的值，例如 `myResourceGroup`、`mystorageaccount`和 `mydisks`。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>准备 VM

Azure 支持各种 Linux 分发（请参阅 [Endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（认可的分发））。 以下文章介绍了如何准备 Azure 支持的各种 Linux 分发版：

* [基于 CentOS 的分发版](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [其他：非认可的分发版](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

另请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 映像的一般提示。

> [!NOTE]
> 只有在使用某个认可的分发的时候也使用 [Azure 认可的分发中的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中“支持的版本”下指定的配置详细信息时，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才适用于运行 Linux 的 VM。
> 
> 

## <a name="option-1-upload-a-vhd"></a>选项 1：上传 VHD

你现在可以将 VHD 直接上传到托管磁盘。 有关说明，请参阅[使用 Azure CLI 将 VHD 上传到 Azure](disks-upload-vhd-to-managed-disk-cli.md)。

## <a name="option-2-copy-an-existing-vm"></a>选项 2：复制现有的 VM

也可以在 Azure 中创建自定义的 VM，然后复制 OS 磁盘并将其附加到新 VM 以创建另一个副本。 这种做法在测试中不会有任何问题，但若要将现有 Azure VM 作为多个新 VM 的模型，请改为创建映像。 有关从现有 Azure VM 创建映像的详细信息，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)。

如果要将现有 VM 复制到其他区域，你可能想要使用 azcopy[在另一区域中的磁盘副本](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)。 

否则，你应拍摄 VM 的快照，然后从快照创建新的操作系统 VHD。

### <a name="create-a-snapshot"></a>创建快照

此示例在资源组 *myResourceGroup* 中创建名为 *myVM* 的 VM 的快照，并创建名为 *osDiskSnapshot* 的快照。

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>创建托管磁盘

从快照创建新的托管磁盘。

获取快照的 ID。 在此示例中，快照名为 *osDiskSnapshot*，位于 *myResourceGroup* 资源组中。

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

创建托管磁盘。 此示例将从快照创建名为 *myManagedDisk* 的托管磁盘，该磁盘位于标准存储中，大小为 128 GB。

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>创建 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM，并将托管磁盘附加为 OS 磁盘 (--attach-os-disk)。 以下示例使用基于上传的 VHD 创建的托管磁盘创建名为 *myNewVM* 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

现在，应该可以使用凭据通过 SSH 从源 VM 连接到该 VM。 

## <a name="next-steps"></a>后续步骤
准备好并上传自定义虚拟磁盘之后，可以阅读有关使用 [Resource Manager](../../azure-resource-manager/management/overview.md) 和模板的详细信息。 可能还需要向新 VM [添加数据磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果需要访问在 VM 上运行的应用程序，请务必[打开端口和终结点](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
