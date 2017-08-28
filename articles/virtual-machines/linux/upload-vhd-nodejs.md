---
title: "使用 Azure CLI 1.0 上传自定义 Linux 映像 | Microsoft 文档"
description: "使用 Resource Manager 部署模型和 Azure CLI 1.0 创建包含自定义 Linux 映像的虚拟硬盘 (VHD) 并将其上传到 Azure。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ca4c6cb9296028275b2b032af0c94baabeec1223
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 上传自定义磁盘映像并从其创建 Linux VM
本文说明如何使用 Resource Manager 部署模型将虚拟硬盘 (VHD) 上传到 Azure，并从此自定义映像创建 Linux VM。 此功能可让你安装并配置 Linux 分发以满足需求，然后使用该 VHD 快速创建 Azure 虚拟机 (VM)。


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI


## <a name="quick-commands"></a>快速命令
如果需要快速完成任务，请参阅以下部分，其中详细说明了用于将 VM 上传到 Azure 的基本命令。 本文档的余下部分（[从此处开始](#requirements)）提供了每个步骤的更详细信息和上下文。

确保已登录 [Azure CLI 1.0](../../cli-install-nodejs.md) 并使用 Resource Manager 模式：

```azurecli
azure config mode arm
```

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`mystorageaccount` 和 `myimages`。

首先创建一个资源组。 以下示例在 `WestUs` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
azure group create myResourceGroup --location "WestUS"
```

创建一个用于存放虚拟磁盘的存储帐户。 以下示例创建名为 `mystorageaccount` 的存储帐户：

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

列出存储帐户的访问密钥。 记下 `key1`：

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

使用获取的存储密钥在存储帐户中创建一个容器。 以下示例使用 `key1` 中的存储密钥值创建名为 `myimages` 的容器：

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

最后，将 VHD 上传到创建的容器。 在 `/path/to/disk/mydisk.vhd` 下指定 VHD 的本地路径：

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

现在，可以使用 [Resource Manager 模板从上传的虚拟磁盘创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)。 也可以使用 CLI 指定磁盘的 URI (`--image-urn`)。 以下示例使用前面上载的虚拟磁盘创建名为 `myVM` 的 VM：

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

目标存储帐户必须与虚拟磁盘上传到的位置相同。 还需要指定或根据提示输入 `azure vm create` 命令所需的所有其他参数，例如虚拟网络、公共 IP 地址、用户名和 SSH 密钥。 阅读有关[可用 CLI Resource Manager 参数](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)的详细信息。

## <a name="requirements"></a>要求
若要完成以下步骤，需要：

* **安装在 .vhd 文件中的 Linux 操作系统** - 将 [Azure 认可的 Linux 分发](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（或参阅[关于未认可分发的信息](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）安装在 VHD 格式的虚拟磁盘中。 可使用多种工具创建 VM 和 VHD：
  * 安装并配置 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，并注意使用 VHD 作为映像格式。 如果需要，可以使用 `qemu-img convert` [转换映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
  * 也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支持更新的 VHDX 格式。 创建 VM 时，请将 VHD 指定为映像格式。 如果需要，可以使用 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet 将 VHDX 磁盘转换为 VHD。 此外，Azure 不支持上传动态 VHD，因此，上传之前，你需要将此类磁盘转换为静态 VHD。 可以使用 [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) 等工具在上载到 Azure 的过程中转换动态磁盘。
> 
> 

* 从自定义映像创建的 VM 必须位于映像本身所在的存储帐户中
  * 创建存储帐户和容器，以存放自定义映像以及所创建的 VM
  * 创建所有 VM 之后，可以安全地删除映像

确保已登录 [Azure CLI 1.0](../../cli-install-nodejs.md) 并使用 Resource Manager 模式：

```azurecli
azure config mode arm
```

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`mystorageaccount` 和 `myimages`。

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>准备要上传的映像
Azure 支持各种 Linux 分发（请参阅 [Endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（认可的分发））。 以下文章指导如何准备 Azure 上支持的各种 Linux 分发：

* **[基于 CentOS 的分发](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[其他 - 非认可分发](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

另请参阅 **[Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)**，以获取更多有关如何为 Azure 准备 Linux 映像的一般提示。

> [!NOTE]
> 只有在使用某个认可的分发的时候也使用 [Azure 认可的分发中的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中“支持的版本”下指定的配置详细信息时，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才适用于运行 Linux 的 VM。


## <a name="create-a-resource-group"></a>创建资源组
资源组以逻辑方式将所有 Azure 资源（例如虚拟网络和存储）聚集在一起，以支持虚拟机。 从此处了解有关 [Azure 资源组](../../azure-resource-manager/resource-group-overview.md)的详细信息。 在上传自定义磁盘映像和创建 VM 之前，首先需要创建一个资源组。 

以下示例在 `WestUS` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>创建存储帐户
VM 以页 Blob 形式存储在存储帐户中。 从此处了解有关 [Azure Blob 存储](../../storage/common/storage-introduction.md#blob-storage)的详细信息。 为自定义磁盘映像和 VM 创建存储帐户。 从自定义磁盘映像创建的所有 VM 都必须位于该映像所在的存储帐户中。

以下示例在前面创建的资源组中创建名为 `mystorageaccount` 的存储帐户：

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>列出存储帐户密钥
Azure 将为每个存储帐户生成两个 512 位的访问密钥。 在向存储帐户进行身份验证以执行操作（例如执行写入操作）时，将使用这些访问密钥。 从此处了解有关[管理对存储的访问](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)的详细信息。 可以使用 `azure storage account keys list` 命令查看访问密钥。

查看创建的存储帐户的访问密钥：

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
记下 `key1`，你会在后续步骤中使用它与存储帐户进行交互。

## <a name="create-a-storage-container"></a>创建存储容器
在存储帐户内创建容器以整理虚拟磁盘和映像，其方式与创建各种目录以便通过逻辑方式整理本地文件系统相同。 一个存储帐户可以包含任意数目的容器。 

以下示例通过指定上一步骤中获取的访问密钥 (`key1`) 创建名为 `myimages` 的容器：

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>上传 VHD
现在可以真正地上传自定义磁盘映像。 与 VM 所用的所有虚拟磁盘一样，需要上传自定义磁盘映像并将其作为页 Blob 存储。

指定访问密钥、在上一步中创建的容器，以及自定义磁盘映像在本地计算机上的路径：

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>从自定义映像创建 VM
从自定义磁盘映像创建 VM 时，需指定磁盘映像的 URI。 确保目标存储帐户与用于存储自定义磁盘映像的位置相匹配。 可以使用 Azure CLI 或 Resource Manager JSON 模板创建 VM。

### <a name="create-a-vm-using-the-azure-cli"></a>使用 Azure CLI 创建 VM
在 `azure vm create` 命令中指定 `--image-urn` 参数，指向自定义磁盘映像。 确保 `--storage-account-name` 与用于存储自定义磁盘映像的存储帐户匹配。 不需要使用与自定义磁盘映像相同的容器来存储 VM。 上传自定义磁盘映像之前，请确保使用前面步骤中所述的相同方式创建任何附加容器。

以下示例从自定义磁盘映像创建名为 `myVM` 的 VM：

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

仍需要指定或根据提示输入 `azure vm create` 命令所需的所有其他参数，例如虚拟网络、公共 IP 地址、用户名和 SSH 密钥。 阅读有关[可用 CLI Resource Manager 参数](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)的详细信息。

### <a name="create-a-vm-using-a-json-template"></a>使用 JSON 模板创建 VM
Azure Resource Manager 模板是用于定义希望生成的环境的 JavaScript 对象表示法 (JSON) 文件。 这些模板细分为不同的资源提供程序，如计算或网络。 可以使用现有模板，也可以编写自己的模板。 阅读有关[使用 Resource Manager 和模板](../../azure-resource-manager/resource-group-overview.md)的详细信息。

在模板的 `Microsoft.Compute/virtualMachines` 提供程序中有一个 `storageProfile` 节点，其中包含 VM 的配置详细信息。 需要编辑的两个主要参数为 `image` 和 `vhd` URI，它们指向自定义磁盘映像和新 VM 的虚拟磁盘。 下面显示了使用自定义磁盘映像的 JSON 示例：

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

可以使用[此现有模板从自定义映像创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)，或阅读有关[创建自己的 Azure Resource Manager 模板](../../azure-resource-manager/resource-group-authoring-templates.md)的信息。 

一旦配置了模板，就可以使用 `azure group deployment create` 命令创建 VM。 使用 `--template-uri` 参数指定 JSON 模板的 URI：

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

如果在计算机上以本地方式存储了一个 JSON 文件，则可以改用 `--template-file` 参数：

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>后续步骤
准备好并上传自定义虚拟磁盘之后，可以阅读有关使用 [Resource Manager](../../azure-resource-manager/resource-group-overview.md) 和模板的详细信息。 可能还需要向新 VM [添加数据磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果需要访问在 VM 上运行的应用程序，请务必[打开端口和终结点](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


