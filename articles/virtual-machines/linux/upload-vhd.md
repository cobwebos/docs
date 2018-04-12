---
title: 使用 Azure CLI 2.0 上传或复制自定义 Linux VM | Microsoft Docs
description: 使用 Resource Manager 部署模型和 Azure CLI 2.0 上传或复制自定义的虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: fc3d72ace6398b69a5efa5543c590bba166baaf0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 从自定义磁盘创建 Linux VM

<!-- rename to create-vm-specialized -->

本文说明如何在 Azure 中上传自定义的虚拟硬盘 (VHD) 或复制现有 VHD，并从自定义磁盘创建 Linux 虚拟机 (VM)。 可以根据要求安装并配置 Linux 分发版，然后使用该 VHD 快速创建新的 Azure 虚拟机。

如果想要从自定义磁盘创建多个 VM，则应该从 VM 或 VHD 创建映像。 有关详细信息，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)。

可以使用两个选项：
* [上传 VHD](#option-1-upload-a-specialized-vhd)
* [复制现有的 Azure VM](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>快速命令

使用 [az vm create](/cli/azure/vm#az_vm_create) 从自定义或专用的磁盘创建新 VM 时，请**附加**该磁盘 (--attach-os-disk)，而不要指定自定义映像或 Marketplace 映像 (--image)。 以下示例使用从自定义 VHD 创建的、名为 *myManagedDisk* 的托管磁盘，创建名为 *myVM* 的 VM：

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>要求
若要完成以下步骤，需要：

* 已准备好在 Azure 中使用的 Linux 虚拟机。 本文的[准备 VM](#prepare-the-vm) 部分介绍了如何查找有关安装 Azure Linux 代理 (waagent) 的特定于分发版的信息。要使 VM 在 Azure 中正常工作，以及要通过 SSH 连接到 VM，需要用到该代理。
* 用于将 [Azure 认可的 Linux 分发版](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（或参阅[关于未认可分发版的信息](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）安装到采用 VHD 格式的虚拟磁盘的 VHD 文件。 可使用多种工具创建 VM 和 VHD：
  * 安装并配置 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，并注意使用 VHD 作为映像格式。 如果需要，可以使用 **qemu-img convert** [转换映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
  * 也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支持更新的 VHDX 格式。 创建 VM 时，请将 VHD 指定为映像格式。 如果需要，可以使用 [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet 将 VHDX 磁盘转换为 VHD。 此外，Azure 不支持上传动态 VHD，因此，上传之前，你需要将此类磁盘转换为静态 VHD。 可以使用 [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) 等工具在上载到 Azure 的过程中转换动态磁盘。
> 
> 


* 确保已安装了最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 *myResourceGroup*、*mystorageaccount* 和 *mydisks*。

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>准备 VM

Azure 支持各种 Linux 分发（请参阅 [Endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（认可的分发））。 以下文章指导如何准备 Azure 上支持的各种 Linux 分发：

* [基于 CentOS 的分发版](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [其他 - 非认可分发版](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

另请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 映像的一般提示。

> [!NOTE]
> 只有在使用某个认可的分发的时候也使用 [Azure 认可的分发中的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中“支持的版本”下指定的配置详细信息时，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才适用于运行 Linux 的 VM。
> 
> 

## <a name="option-1-upload-a-vhd"></a>选项 1：上传 VHD

可以上传本地计算机上运行的或者从另一个云导出的自定义 VHD。 若要使用 VHD 创建新的 Azure VM，需要将 VHD 上传到存储帐户，并从该 VHD 创建托管磁盘。 

### <a name="create-a-resource-group"></a>创建资源组

在上载自定义磁盘和创建 VM 之前，首先需要使用 [az group create](/cli/azure/group#az_group_create) 创建一个资源组。

以下示例在 *eastus* 位置创建名为 *myResourceGroup* 的资源组：[Azure 托管磁盘概述](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>创建存储帐户

可以使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 为自定义磁盘和 VM 创建存储帐户。 

以下示例在前面创建的资源组中创建名为 *mystorageaccount* 的存储帐户：

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>列出存储帐户密钥
Azure 将为每个存储帐户生成两个 512 位的访问密钥。 在向存储帐户进行身份验证以执行操作（例如执行写入操作）时，将使用这些访问密钥。 从此处了解有关[管理对存储的访问](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)的详细信息。 可使用 [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 查看访问密钥。

查看创建的存储帐户的访问密钥：

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

输出类似于：

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
记下 **key1**，因为在后续步骤中需要使用它来与存储帐户交互。

### <a name="create-a-storage-container"></a>创建存储容器
在存储帐户中创建用于整理磁盘的容器的方式，与创建各种目录以便有条理地整理本地文件系统的方式相同。 一个存储帐户可以包含任意数目的容器。 可以使用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 创建容器。

以下示例创建名为 *mydisks* 的容器：

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>上传 VHD
现在，使用 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 上传自定义磁盘。 可以页 Blob 的形式上传和存储自定义磁盘。

指定访问密钥、在上一步中创建的容器，以及自定义磁盘在本地计算机上的路径：

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
上传 VHD 可能需要一些时间。

### <a name="create-a-managed-disk"></a>创建托管磁盘


使用 [az disk create](/cli/azure/disk#az_disk_create) 从 VHD 创建托管磁盘。 以下示例从已上传到命名存储帐户和容器的 VHD 创建名为 *myManagedDisk* 的托管磁盘：

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>选项 2：复制现有 VM

也可以在 Azure 中创建自定义的 VM，然后复制 OS 磁盘并将其附加到新 VM 以创建另一个副本。 这种做法在测试中不会有任何问题，但若要将现有 Azure VM 作为多个新 VM 的模型，则必须改为创建**映像**。 有关从现有 Azure VM 创建映像的详细信息，请参阅[使用 CLI 创建 Azure VM 的自定义映像](tutorial-custom-images.md)

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

创建托管磁盘。 在此示例中，我们将在标准存储中从快照创建大小为 128GB、名为 *myManagedDisk* 的托管磁盘。

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>创建 VM

现在，使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM，并将托管磁盘附加为 OS 磁盘 (--attach-os-disk)。 以下示例使用基于上传的 VHD 创建的托管磁盘创建名为 *myNewVM* 的 VM：

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
准备好并上传自定义虚拟磁盘之后，可以阅读有关使用 [Resource Manager](../../azure-resource-manager/resource-group-overview.md) 和模板的详细信息。 可能还需要向新 VM [添加数据磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果需要访问在 VM 上运行的应用程序，请务必[打开端口和终结点](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

