---
title: "使用 SMB 在 Linux VM 上装载 Azure 文件存储 | Microsoft 文档"
description: "如何通过 Azure CLI 2.0（预览版）使用 SMB 在 Linux VM 上装载 Azure 文件存储"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>通过 Azure CLI 2.0（预览版）使用 SMB 在 Linux VM 上装载 Azure 文件存储

本文说明如何使用 SMB 装载利用 Linux VM 上的 Azure 文件存储服务。 Azure 文件存储使用标准 SMB 协议在云中提供文件共享。  要求如下：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于经典部署模型和资源管理部署模型的 CLI
- [Azure CLI 2.0（预览版）](#quick-commands)- 适用于资源管理部署模型的下一代 CLI（本文）


## <a name="quick-commands"></a>快速命令

如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。

先决条件：资源组、VNet、将 SSH 入站的 NSG、子网、Azure 存储帐户、Azure 存储帐户密钥、Azure 文件存储共享和 Linux VM。 将所有示例替换为自己的值。

为本地装载创建目录，如下所示：

```bash
mkdir -p /mnt/mymountpoint
```

将 Azure 文件存储 SMB 共享装载到装载点，如下所示：

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

若要在重新启动后保留装载，请向 `/etc/fstab` 添加一行，如下所示：

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>详细演练

Azure 文件存储使用标准 SMB 协议在云中提供文件共享。 并且使用最新版本的文件存储，还可以从支持 SMB 3.0 的任何 OS 装载文件共享。 在 Linux 上使用 SMB 装载可轻松备份到 SLA 支持的可靠、永久的存档存储位置。  

将 VM 中的文件移到 Azure 文件存储上托管的 SMB 装载是调试日志的好办法，因为相同的 SMB 共享可本地装载到 Mac、Linux 或 Windows 工作站。 SMB 不会是实时流式处理 Linux 或应用程序日志的最佳解决方案，因为 SMB 协议并非为那样重的日志记录职责而构建。 专用统一的日志记录层工具（如 Fluentd）会是 SMB 之上的更好选择，可收集 Linux 和应用程序日志记录输出。

在此详细的演练中，我们创建所需的先决条件，即先创建 Azure 文件存储共享，然后通过 SMB 将其装载到 Linux VM 上。

首先，使用 [az group create](/cli/azure/group#create) 创建资源组，以用于保存文件共享。 以下示例在 `West US` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>创建 Azure 存储帐户
接下来，使用 [az storage account create](/cli/azure/storage/account#create) 创建存储帐户，以用于存储实际文件。 以下示例使用 `Standard_LRS` 存储 SKU 创建名为 `mystorageaccount` 的存储帐户：

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>显示存储帐户密钥

创建存储帐户时，将成对创建 Azure 存储帐户密钥。 之所以成对创建存储帐户密钥是为了不中断任何服务就可轮换密钥。 将密钥轮换到密钥对中的第二个密钥后，将创建新的密钥对。 新的存储帐户密钥始终成对创建，可确保始终至少有一个未使用的存储密钥可以轮换到。

使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 查看存储帐户密钥。 以下示例列出了名为 `mystorageaccount` 的存储帐户的密钥：

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

若要提取单个密钥，请使用 `--query` 标志。 以下示例提取了第一个密钥 (`[0]`)：

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>创建 Azure 文件存储共享

使用 [az storage share create](/cli/azure/storage/share#create) 创建包含 SMB 共享的文件存储共享。 配额始终以千兆字节 (GB) 为单位。 传递前面的 **az storage account keys list** 命令中的一个密钥。 以下示例创建名为 `mystorageshare`、具有 `10` GB 配额的共享。

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>创建装入点目录

在 Linux 文件系统上需要有要将 SMB 共享装载到的本地目录。 写入到本地装载目录或从本地装载目录读取的任何内容都将转发到 Azure 文件存储上托管的 SMB 共享。 以下示例创建位于 `/mnt/mymountdirectory` 的本地目录：

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>装载 SMB 共享
将 SMB 共享装载到你创建的本地目录，如下所示。 提供自己的装载凭据的存储帐户用户名和存储帐户密钥，如下所示：

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>重新启动后保留 SMB 装载

重新启动 Linux VM 后，已装载的 SMB 共享会在关闭过程中卸载。 若要在启动时重新装载 SMB 共享，请向 Linux `/etc/fstab` 添加一行。 Linux 使用 `fstab` 文件列出它需要在启动期间装载哪些文件系统。 添加 SMB 共享可确保 Azure 文件存储共享是 Linux VM 的永久装载文件系统。 使用 `cloud-init` 可以将 Azure 文件存储 SMB 共享添加到新的 VM。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>后续步骤

- [在创建期间使用 cloud-init 自定义 Linux VM](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [将磁盘添加到 Linux VM](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 Azure CLI 加密 Linux VM 上的磁盘](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

