---
title: "创建 Linux VHD 并将其上传到 Azure | Microsoft Docs"
description: "使用经典部署模型创建并上传包含 Linux 操作系统的 Azure 虚拟硬盘 (VHD)"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 23c30c954875598ce3e01db137b0ef8cda9779f4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>创建并上传包含 Linux 操作系统的虚拟硬盘
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 还可以[使用 Azure Resource Manager 上载自定义磁盘映像](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文介绍如何创建和上传虚拟硬盘 (VHD)，以便可以使用它作为自己的映像在 Azure 中创建虚拟机。 学习如何准备操作系统，以便使用它来基于该映像创建多个虚拟机。 


## <a name="prerequisites"></a>先决条件
本文假设拥有以下项目：

* **安装在 .vhd 文件中的 Linux 操作系统** - 已将 [Azure 认可的 Linux 分发](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（或参阅[关于未认可分发的信息](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）安装在 VHD 格式的虚拟磁盘中。 可使用多种工具创建 VM 和 VHD：
  * 安装并配置 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，并注意使用 VHD 作为映像格式。 如果需要，可以使用 `qemu-img convert` [转换映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
  * 也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支持更新的 VHDX 格式。 创建 VM 时，请将 VHD 指定为映像格式。 如果需要，可以使用 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet 将 VHDX 磁盘转换为 VHD。 此外，Azure 不支持上传动态 VHD，因此，上传之前，你需要将此类磁盘转换为静态 VHD。 可以使用 [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) 等工具在上传到 Azure 的过程中转换动态磁盘。

* **Azure 命令行接口** - 安装最新的 [Azure 命令行接口](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)以上载 VHD。

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>步骤 1：准备要上传的映像
Azure 支持各种 Linux 分发（请参阅 [Endorsed Distributions](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（认可的分发））。 以下文章将指导用户如何准备 Azure 上支持的各种 Linux 分发。 完成以下指南中的步骤后，返回到此处，应该有了一个可以上传到 Azure 的 VHD 文件：

* **[基于 CentOS 的分发版](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 和 openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[其他 - 非认可分发版](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> 只有在使用某个认可的分发的时候也使用 [Azure 认可的分发中的 Linux](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中“支持的版本”下指定的配置详细信息时，Azure 平台 SLA 才适用于运行 Linux 操作系统的虚拟机。 Azure 映像库中的所有 Linux 分发都是具有所需配置的认可的分发。
> 
> 

另请参阅 **[Linux 安装说明](../create-upload-generic.md#general-linux-installation-notes)**，以获取更多有关如何为 Azure 准备 Linux 映像的一般提示。

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>步骤 2：准备连接到 Azure
请确保在经典部署模型中使用 Azure CLI (`azure config mode asm`)，并登录帐户：

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>步骤 3：向 Azure 上传映像
需要一个存储帐户，以便向其上传 VHD 文件。 可以选取现有存储帐户，也可以[创建新的存储帐户](../../../storage/common/storage-create-storage-account.md)。

在 Azure CLI 中使用以下命令来上传映像：

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

在上述示例中：

* **BlobStorageURL** 是要使用的存储帐户的 URL
* **YourImagesFolder** 是 Blob 存储中用于存储映像的容器
* **VHDName** 是在门户中显示用于标识虚拟硬盘的标签。
* **PathToVHDFile** 是 .vhd 文件在计算机上的完整路径和名称。

以下命令显示一个完整示例：

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>步骤 4：从映像创建 VM
使用 `azure vm create` 以与创建常规 VM 相同的方式创建 VM。 指定在上一步中为映像提供的名称。 在以下示例中，使用上一步中指定的映像名称 **myImage**：

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

若要创建自己的 VM，请提供自己的用户名 + 密码、位置、DNS 名称和映像名称。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅[用于 Azure 经典部署模型的 Azure CLI 参考](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload

