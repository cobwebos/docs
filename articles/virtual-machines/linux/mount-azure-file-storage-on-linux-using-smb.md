---
title: 使用 SMB 在 Linux VM 上装载 Azure 文件存储 | Microsoft Docs
description: 如何通过 Azure CLI 使用 SMB 在 Linux VM 上装载 Azure 文件存储
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 2019324030b2e4c469d0b9ba937fb40a9d0675f1
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099705"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>使用 SMB 在 Linux VM 上装载 Azure 文件存储


本文说明如何通过 Azure CLI 使用 SMB 装载利用 Linux VM 上的 Azure 文件存储服务。 Azure 文件存储使用标准 SMB 协议在云中提供文件共享。 

文件存储使用标准 SMB 协议在云中提供文件共享。 可以从支持 SMB 3.0 的任何 OS 装载文件共享。 在 Linux 上使用 SMB 装载时，可轻松备份到 SLA 支持的可靠、永久的存档存储位置。

将文件从 VM 移至托管在文件存储上的 SMB 装载，可以很轻松地调试日志。 同一 SMB 共享可以通过本地方式装载到 Mac、Linux 或 Windows 工作站。 SMB 不会是实时流式处理 Linux 或应用程序日志的最佳解决方案，因为 SMB 协议并非为处理那样重的日志记录任务而构建。 专用统一的日志记录层工具（如 Fluentd）会是 SMB 之上的更好选择，可收集 Linux 和应用程序日志记录输出。

本指南需要运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 **az --version**。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>创建资源组

在“美国东部”位置创建一个名为“myResourceGroup”的资源组。

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>创建存储帐户

使用 [az storage account create](/cli/azure/storage/account#create) 在创建的资源组中创建一个新存储帐户。 此示例创建名为 *mySTORAGEACCT<random number>* 的存储帐户，然后将该存储帐户的名称置于变量 **STORAGEACCT** 中。 存储帐户名称必须唯一，请使用 `$RANDOM` 将一个数字追加到名称末尾，使之变得唯一。

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>获取存储密钥

创建存储帐户时，帐户密钥是成对创建的，这样是为了不中断任何服务就可轮换密钥。 轮换到密钥对中的第二个密钥后，将创建新的密钥对。 新的存储帐户密钥始终成对创建，因此始终至少有一个未使用的存储帐户密钥可以切换到。

使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 查看存储帐户密钥。 此示例将密钥 1 的值存储在 **STORAGEKEY** 变量中。

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>创建文件共享

使用 [az storage share create](/cli/azure/storage/share#create) 创建文件存储共享。 

共享名必须全部采用小写字母、数字和单个连字符，但不能以连字符开头。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

此示例创建名为 *myshare* 且具有 10-GiB 配额的共享。 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>创建装入点

若要在 Linux 计算机上装载 Azure 文件共享，需要确保已安装 **cifs-utils** 包。 有关安装说明，请参阅[安装适用于 Linux 发行版的 cifs-utils 包](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils)。

Azure 文件使用通过 TCP 端口 445 进行通信的 SMB 协议。  如果无法装载 Azure 文件共享，请确保防火墙未阻止 TCP 端口 445。


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>装载共享

将 Azure 文件共享装载到本地目录中。 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```



## <a name="persist-the-mount"></a>持久保留装载

重新启动 Linux VM 后，已装载的 SMB 共享会在关闭过程中卸载。 若要在启动时重新装载 SMB 共享，请向 Linux /etc/fstab 添加一行。 Linux 使用 fstab 文件列出在启动过程中需要装载的文件系统。 添加 SMB 共享可确保文件存储共享是 Linux VM 的永久装载文件系统。 使用 cloud-init 可以将文件存储 SMB 共享添加到新的 VM。

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
为了提高生产环境的安全性，应将凭据存储在 fstab 之外。

## <a name="next-steps"></a>后续步骤

- [在创建期间使用 cloud-init 自定义 Linux VM](using-cloud-init.md)
- [将磁盘添加到 Linux VM](add-disk.md)
- [使用 Azure CLI 加密 Linux VM 上的磁盘](encrypt-disks.md)

