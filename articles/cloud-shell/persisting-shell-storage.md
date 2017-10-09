---
title: "为 Azure Cloud Shell（预览版）中的 Bash 持久保存文件 | Microsoft Docs"
description: "演练 Azure Cloud Shell 中的 Bash 如何持久保存文件。"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8b8a82e1c1328d952a85ea5afd975a95f5a6e740
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell 存储的工作原理 
Cloud Shell 通过以下两种方法持久保存文件： 
* 创建 `$Home` 目录的磁盘映像来持久保持目录中所有内容。 磁盘映像将作为 `acc_<User>.img` 保存在指定的文件共享中，位于以下位置：`fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`，并会自动同步更改。 
* 将指定的文件共享装载为 `$Home` 目录中的 `clouddrive` 以便直接进行文件共享交互。 `/Home/<User>/clouddrive` 映射到 `fileshare.storage.windows.net/fileshare`。
 
> [!NOTE]
> `$Home` 目录中的所有文件（如 SSH 密钥）将持久保存用户磁盘映像（存储于已装载的文件共享中）中。 在 `$Home` 目录和已装载的文件共享中持久保存信息时，请应用最佳做法。

## <a name="use-the-clouddrive-command"></a>使用 `clouddrive` 命令
使用 Cloud Shell 中的 Bash，可运行一个名为 `clouddrive` 的命令，手动更新装载到 Cloud Shell 的文件共享。
![运行“clouddrive”命令](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>装载新的 clouddrive

### <a name="prerequisites-for-manual-mounting"></a>手动装载的先决条件
可使用 `clouddrive mount` 命令更新与 Cloud Shell 相关联的文件共享。

如果装载现有的文件共享，则存储帐户必须：
* 是支持文件共享的本地冗余存储或异地冗余存储。
* 位于分配的区域中。 在装入时，被分配到的区域将列在资源组名称 `cloud-shell-storage-<region>` 中。

### <a name="the-clouddrive-mount-command"></a>clouddrive 装载命令

> [!NOTE]
> 装载新的文件共享时，将为 `$Home` 目录创建新的用户映像。 以前的 `$Home` 映像保留在以前的文件共享中。

使用以下参数运行 `clouddrive mount` 命令：

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要查看更多详细信息，请运行 `clouddrive mount -h`，如下所示：

![运行 `clouddrive mount` 命令](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>卸载 clouddrive
随时可以卸载已装载到 Cloud Shell 的文件共享。 由于 Cloud Shell 需要使用已装载的文件共享，因此，在下一个会话中，系统会提示创建并装载另一个文件共享。

1. 运行 `clouddrive unmount`。
2. 确认提示。

除非手动删除，否则文件共享将一直存在。 Cloud Shell 在后续会话中不会再搜索此文件共享。 若要查看更多详细信息，请运行 `clouddrive unmount -h`，如下所示：

![运行 `clouddrive unmount` 命令](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> 虽然运行此命令不会删除任何资源，但手动删除映射到 Cloud Shell 的资源组、存储帐户或文件共享将会清除 `$Home` 目录磁盘映像和文件共享中的任何文件。 此操作不可撤消。

## <a name="list-clouddrive"></a>列出 `clouddrive`
若要查明哪些文件共享已装载为 `clouddrive`，请运行 `df` 命令。 

clouddrive 的文件路径会在 URL 中显示存储帐户名称和文件共享。 例如： `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) <br>
[了解 Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解存储标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

