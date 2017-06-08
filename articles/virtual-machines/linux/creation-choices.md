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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>创建 Linux VM 的不同方式
用户可以在 Azure 中灵活地使用适合自己的工具和工作流创建 Linux 虚拟机 (VM)。 本文总结了创建 Linux VM 的相关差异和示例，包括 Azure CLI 2.0。 还可以查看包括 [Azure CLI 1.0](creation-choices-nodejs.md) 在内的创建选项。

[Azure CLI 2.0](/cli/azure/install-az-cli2) 可通过 npm 包、发行版提供的程序包或 Docker 容器跨平台使用。 为环境安装最适当的内部版本，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户

* [使用 Azure CLI 2.0 创建 Linux VM](quick-create-cli.md)
  
  * 使用 [az group create](/cli/azure/group#create) 创建名为 *myResourceGroup* 的资源组： 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * 使用 [az vm create](/cli/azure/vm#create) 通过最新的 *UbuntuLTS* 映像创建名为 *myVM* 的 VM，并生成 SSH 密钥（如果 *~/.ssh* 中尚无此类密钥）：

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [使用 Azure 模板创建 Linux VM](create-ssh-secured-vm-from-template.md)
  
  * 以下示例使用 [az group deployment create](/cli/azure/group/deployment#create) 基于 GitHub 上存储的一个模板创建 VM：
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [创建 Linux VM 并使用 cloud-init 进行自定义](tutorial-automate-vm-deployment.md)

* [在多个 Linux VM 上创建负载均衡且具有高可用性的应用程序](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Azure 门户
[Azure 门户](https://portal.azure.com)允许用户快速创建 VM，因为不需在系统上安装内容。 使用 Azure 门户创建 VM：

* [使用 Azure 门户创建 Linux VM](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>操作系统和映像选项
创建 VM 时，可根据要运行的操作系统选择映像。 Azure 及其合作伙伴提供了许多映像，其中一些映像包括预安装的应用程序和工具。 也可上传自己的某个映像（请参阅[以下部分](#use-your-own-image)）。

### <a name="azure-images"></a>Azure 映像
使用 [az vm image](/cli/azure/vm/image) 命令，按发布者、发行版本和内部版本查看可用的内容。

列出可用的发布者：

```azurecli
az vm image list-publishers --location eastus
```

列出给定发布者的可用产品（服务）：

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

列出给定产品/服务的可用 SKU（发行版本）：

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

列出给定发行版的所有可用映像：

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

请参阅[使用 Azure CLI 导航并选择 Azure 虚拟机映像](cli-ps-findimage.md)，获取有关浏览和使用可用映像的更多示例。

[az vm create](/cli/azure/vm#create) 命令提供的别名可用于快速访问较常见的发行版及其最新版本。 使用别名比每次创建 VM 时都指定发布者、产品/服务、SKU 和版本要快速：

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
如果需要特定自定义，可以通过捕获现有的 Azure VM 使用基于该 VM 的映像。 也可以上传本地创建的映像。 有关受支持的发行版以及如何使用你自己的映像的详细信息，请参阅以下文章：

* [Azure endorsed distributions（Azure 认可的分发版）](endorsed-distros.md)
* [Information for non-endorsed distributions（有关未认可分发版的信息）](create-upload-generic.md)
* [如何基于现有 Azure VM 创建映像](tutorial-custom-images.md)。
  
  * 下面是用于基于现有 Azure VM 创建映像的快速入门示例命令：
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>后续步骤
* 通过 [CLI](quick-create-cli.md)、从[门户](quick-create-portal.md)或使用 [Azure Resource Manager 模板](../windows/cli-deploy-templates.md)创建 Linux VM。
* 创建 Linux VM 后，[了解 Azure 磁盘和存储](tutorial-manage-disks.md)。
* [重置密码或 SSH 密钥并管理用户](using-vmaccess-extension.md)。

