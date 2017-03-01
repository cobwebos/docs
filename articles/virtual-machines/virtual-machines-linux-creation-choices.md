---
title: "在 Azure 中创建 Linux VM 的不同方式 | Microsoft Azure"
description: "了解在 Azure 上创建 Linux 虚拟机的不同方法，包括指向每种方法的工具和教程的链接。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 23862762fcf0939ce84859fdae0274421c0bb5fe


---
# <a name="different-ways-to-create-a-linux-vm-including-the-azure-cli-20-preview"></a>创建 Linux VM 的不同方式（包括使用 Azure CLI 2.0 预览版）
用户可以在 Azure 中灵活地使用适合自己的工具和工作流创建 Linux 虚拟机 (VM)。 本文总结了创建 Linux VM 的相关差异和示例。

## <a name="azure-cli"></a>Azure CLI
可以使用以下 CLI 版本之一在 Azure 中创建 VM：

- [Azure CLI 1.0](virtual-machines-linux-creation-choices-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI
- Azure CLI 2.0（预览版）- 适用于资源管理部署模型（本文）的下一代 CLI

[Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)可通过 npm 包、发行版提供的程序包或 Docker 容器跨平台使用。 为环境安装最适当的内部版本，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户

以下示例使用 Azure CLI 2.0（预览版）。 请阅读每篇文章，了解有关所示命令的更多详细信息。 也可通过 [Azure CLI 1.0](virtual-machines-linux-creation-choices-nodejs.md) 查找有关 Linux 创建选项的示例。

* [使用 Azure CLI 2.0（预览版）创建 Linux VM](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 此示例使用 [az group create](/cli/azure/group#create) 创建名为 `myResourceGroup` 的资源组： 
    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * 此示例使用 [az vm create](/cli/azure/vm#create) 创建名为 `myVM` 的 VM，将最新 Debian 映像与 Azure 托管磁盘以及名为 `id_rsa.pub` 的公钥配合使用：

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * 若要使用非托管磁盘，请向上面的命令添加 `--use-unmanaged-disks` 标志。 系统将为用户创建存储帐户。 有关详细信息，请参阅 [Azure 托管磁盘概述](../storage/storage-managed-disks-overview.md)。

* [使用 Azure 模板创建受保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 以下示例使用 [az group deployment create](/cli/azure/group/deployment#create) 创建 VM，使用存储在 GitHub 上的模板：
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [使用 Azure CLI 创建完整的 Linux 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 包括在可用性集中创建负载均衡器和多个 VM。

* [将磁盘添加到 Linux VM](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 以下示例使用 [az vm disk attach-new](/cli/azure/vm/disk#attach-new) 将名为 `myDataDisk.vhd` 的 5Gb 非托管磁盘添加到名为 `myVM` 的现有 VM：
  
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://mystorageaccount.blob.core.windows.net/vhds/myDataDisk.vhd
    ```

## <a name="azure-portal"></a>Azure 门户
[Azure 门户](https://portal.azure.com)允许用户快速创建 VM，因为不需在系统上安装内容。 使用 Azure 门户创建 VM：

* [使用 Azure 门户创建 Linux VM](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [使用 Azure 门户附加磁盘](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>操作系统和映像选项
创建 VM 时，可根据要运行的操作系统选择映像。 Azure 及其合作伙伴提供了许多映像，其中一些映像包括预安装的应用程序和工具。 也可上载自己的某个映像（请参阅[以下部分](#use-your-own-image)）。

### <a name="azure-images"></a>Azure 映像
使用 [az vm image](/cli/azure/vm/image) 命令，按发布者、发行版本和内部版本查看可用的内容。

列出可用的发布者：

```azurecli
az vm image list-publishers --location WestUS
```

列出给定发布者的可用产品（服务）：

```azurecli
az vm image list-offers --publisher-name Canonical --location WestUS
```

列出给定产品/服务的可用 SKU（发行版本）：

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer --location WestUS
```

列出给定发行版的所有可用映像：

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

请参阅[使用 Azure CLI 导航并选择 Azure 虚拟机映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，获取有关浏览和使用可用映像的更多示例。

**az vm create** 命令提供的别名可用于快速访问较常见的发行版及其最新版本。 使用别名比每次创建 VM 时都指定发布者、产品/服务、SKU 和版本要快速：

| 别名 | 发布者 | 产品 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Redhat |RHEL |7.2 |最新 |
| SLES |SLES |SLES |12-SP1 |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |

### <a name="use-your-own-image"></a>使用你自己的映像
如果需要特定自定义，可以通过*捕获*现有的 Azure VM 使用基于该 VM 的映像。 也可以上载本地创建的映像。 有关受支持的发行版以及如何使用你自己的映像的详细信息，请参阅以下文章：

* [Azure endorsed distributions（Azure 认可的分发版）](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Information for non-endorsed distributions（有关未认可分发版的信息）](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [如何捕获用作 Resource Manager 模板的 Linux 虚拟机](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
  
  * 用于捕获现有 VM（使用非托管磁盘）的快速入门 **az vm** 示例命令：
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>后续步骤
* 通过[门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Azure Resource Manager 模板](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)创建 Linux VM。
* 创建 Linux VM 后，可[添加数据磁盘](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [重置密码或 SSH 密钥和管理用户](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


