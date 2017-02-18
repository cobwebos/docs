---
title: "创建 Linux VM 的不同方式 | Microsoft Docs"
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
ms.date: 01/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 1cbc3534aa0415a14d8b955dbcd291d2200c276e
ms.openlocfilehash: 2ffc8d1f8ec924914c624f882bf35a08f0dd5888


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>在 Azure 中创建 Linux 虚拟机的不同方式
用户可以在 Azure 中灵活地使用适合自己的工具和工作流创建 Linux 虚拟机 (VM)。 本文总结了创建 Linux VM 的相关差异和示例。

## <a name="azure-cli"></a>Azure CLI
可以使用以下 CLI 版本之一在 Azure 中创建 VM：

- Azure CLI 1.0 – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0（预览版）](virtual-machines-linux-creation-choices.md)- 适用于资源管理部署模型的下一代 CLI

Azure CLI 1.0 可通过 npm 包、发行版提供的程序包或 Docker 容器跨平台使用。 有关详细信息，请参阅 [如何安装和配置 Azure CLI](../xplat-cli-install.md)。 以下教程提供了 Azure CLI 1.0 使用示例。 阅读下面每篇文章，了解更多有关所示的 CLI 快速启动命令的更多详细信息：

* [Create a Linux VM from the Azure CLI for dev and test（从 Azure CLI 创建用于开发和测试的 Linux VM）](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 以下示例使用名为 `azure_id_rsa.pub` 的公共密钥创建 CoreOS VM：
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [使用 Azure 模板创建受保护的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 以下示例使用存储在 GitHub 上的模板创建 VM：
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [使用 Azure CLI 创建完整的 Linux 环境](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 包括在可用性集中创建负载均衡器和多个 VM。
* [将磁盘添加到 Linux VM](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 以下示例将 5Gb 磁盘添加到名为 `TestVM` 的现有 VM：
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure 门户
[Azure 门户](https://portal.azure.com)允许用户快速创建 VM，因为不需在系统上安装内容。 使用 Azure 门户创建 VM：

* [使用 Azure 门户创建 Linux VM](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [使用 Azure 门户附加磁盘](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>操作系统和映像选项
创建 VM 时，可根据要运行的操作系统选择映像。 Azure 及其合作伙伴提供了许多映像，其中一些映像包括预安装的应用程序和工具。 也可上载自己的某个映像（请参阅[以下部分](#use-your-own-image)）。

### <a name="azure-images"></a>Azure 映像
使用 `azure vm image` CLI 命令，按发布者、发行版本和内部版本查看可用的内容。

列出可用的发布者，如下所示：

```azurecli
azure vm image list-publishers --location WestUS
```

列出给定发布者的可用产品（服务），如下所示：

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

列出给定产品/服务的可用 SKU（分发版本），如下所示：

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

列出给定发行版的所有可用映像，如下所示：

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

请参阅[使用 Azure CLI 导航并选择 Azure 虚拟机映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，获取有关浏览和使用可用映像的更多示例。

`azure vm quick-create` 和 `azure vm create` 命令提供的别名可用于快速访问较常见的发行版及其最新版本。 使用别名比每次创建 VM 时都指定发布者、产品/服务、SKU 和版本要快速：

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
* [上载自定义磁盘映像并从其创建 Linux VM](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [如何捕获用作 Resource Manager 模板的 Linux 虚拟机](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
  
  * 用于捕获现有 VM 的快速入门示例命令：
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>后续步骤
* 通过[门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Azure Resource Manager 模板](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)创建 Linux VM。
* 创建 Linux VM 后，可[添加数据磁盘](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [重置密码或 SSH 密钥和管理用户](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO3-->


