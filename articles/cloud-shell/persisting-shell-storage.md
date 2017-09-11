---
title: "在 Azure Cloud Shell（预览版）中持久保存文件 | Microsoft Docs"
description: "演练 Azure Cloud Shell 如何持久保存文件。"
services: 
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
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 61a8bfcf3704f361432400771d8fcc8b81927b53
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中持久保存文件
Cloud Shell 利用 Azure 文件存储在会话之间持久保存文件。

## <a name="set-up-a-clouddrive-file-share"></a>设置 `clouddrive` 文件共享
初始启动时，Cloud Shell 会提示关联新的或现有的文件共享，以便在会话之间持久保存文件。

### <a name="create-new-storage"></a>创建新存储

使用基本设置并仅选择一个订阅时，Cloud Shell 会在最靠近你的支持区域中代表你创建三个资源：
* 资源组：`cloud-shell-storage-<region>`
* 存储帐户：`cs<uniqueGuid>`
* 文件共享：`cs-<user>-<domain>-com-<uniqueGuid>`

![订阅设置](media/basic-storage.png)

文件共享在 `$Home` 目录中装载为 `clouddrive`。 文件共享还用来存储所创建的一个 5-GB 映像，该映像自动更新并持久保存 `$Home` 目录。 这是一次性操作，文件共享会自动装载在后续会话中。

### <a name="use-existing-resources"></a>使用现有资源

通过使用高级选项，可以将现有资源相关联。 出现存储设置的提示时，选择“显示高级设置”查看其他选项。 现有的文件共享收到一个 5 GB 的用户映像，用于持久保存 `$Home` 目录。 从下拉菜单筛选 Cloud Shell 区域、本地冗余和异地冗余存储帐户。

![资源组设置](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>根据 Azure 资源策略限制资源创建
在 Cloud Shell 中创建的存储帐户都标记有 `ms-resource-usage:azure-cloud-shell`。 如果想禁止用户在 Cloud Shell 中创建存储帐户，请创建此特定标记触发的[适用于标记的 Azure 资源策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)。

## <a name="how-cloud-shell-works"></a>Cloud Shell 工作原理
Cloud Shell 通过以下两种方法持久保存文件：
* 创建 `$Home` 目录的磁盘映像来持久保持目录中所有内容。 磁盘映像将作为 `acc_<User>.img` 保存在指定的文件共享中，位于以下位置：`fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`，并会自动同步更改。

* 将指定的文件共享装载为 `$Home` 目录中的 `clouddrive`，以便直接进行文件共享交互。 `/Home/<User>/clouddrive` 映射到 `fileshare.storage.windows.net/fileshare`。
 
> [!NOTE]
> `$Home` 目录中的所有文件（如 SSH 密钥）将持久保存用户磁盘映像（存储于已装载的文件共享中）中。 在 `$Home` 目录和已装载的文件共享中持久保存信息时，请应用最佳做法。

## <a name="use-the-clouddrive-command"></a>使用 `clouddrive` 命令
使用 Cloud Shell，可运行一个名为 `clouddrive` 的命令，手动更新装载到 Cloud Shell 的文件共享。
![运行“clouddrive”命令](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>装载新的 `clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>手动装载的先决条件
可使用 `clouddrive mount` 命令更新与 Cloud Shell 相关联的文件共享。

如果装载现有的文件共享，则存储帐户必须：
* 是支持文件共享的本地冗余存储或异地冗余存储。
* 位于分配的区域中。 在装入时，被分配到的区域将列在资源组名称 `cloud-shell-storage-<region>` 中。

### <a name="supported-storage-regions"></a>支持的存储区域
Azure 文件必须与装载到 Cloud Shell 计算机位于同一区域。 Cloud Shell 集群目前位于以下区域中：
|区域|区域|
|---|---|
|美洲|美国东部、美国中南部、美国西部|
|欧洲|欧洲北部、欧洲西部|
|亚太区|印度中部、亚洲东南部|

### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 命令

> [!NOTE]
> 如果装载新文件共享，会为 `$Home` 目录创建新用户映像，因为以前的 `$Home` 映像保存在以前的文件共享中。

使用以下参数运行 `clouddrive mount` 命令：

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要查看更多详细信息，请运行 `clouddrive mount -h`，如下所示：

![运行 `clouddrive mount` 命令](media/mount-h.png)

## <a name="unmount-clouddrive"></a>卸载 `clouddrive`
随时可以卸载已装载到 Cloud Shell 的文件共享。 卸载文件共享后，进行下一个会话前，系统会提示装载新的文件共享。

从 Cloud Shell 中删除文件共享：
1. 运行 `clouddrive unmount`。
2. 同意并确认提示。

除非手动删除，否则文件共享将一直存在。 Cloud Shell 在后续会话中不会再搜索此文件共享。

若要查看更多详细信息，请运行 `clouddrive unmount -h`，如下所示：

![运行 `clouddrive unmount` 命令](media/unmount-h.png)

> [!WARNING]
> 运行此命令不会删除任何资源。 手动删除映射到 Cloud Shell 的资源组、存储帐户或文件共享会永久删除 `$Home` 目录映像以及文件共享中的任何其他文件。 此操作不可撤消。

## <a name="list-clouddrive-file-shares"></a>列出 `clouddrive` 文件共享
若要查明哪些文件共享已装载为 `clouddrive`，请执行以下 `df` 命令。 

clouddrive 的文件路径会在 URL 中显示存储帐户名称和文件共享。 例如： `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                               1K-blocks     Used Available Use% Mounted on
overlay                                                   30428648 15585636  14826628  52% /
tmpfs                                                       986704        0    986704   0% /dev
tmpfs                                                       986704        0    986704   0% /sys/fs/cgroup
/dev/sda1                                                 30428648 15585636  14826628  52% /etc/hosts
shm                                                          65536        0     65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName        6291456  5242944   1048512  84% /usr/justin/clouddrive
/dev/loop0                                                 5160576   601652   4296780  13% /home/justin
```

## <a name="transfer-local-files-to-cloud-shell"></a>将本地文件传输到 Cloud Shell
`clouddrive` 目录与 Azure 门户存储边栏选项卡同步。 使用此边栏选项卡将本地文件传输到文件共享或从文件共享传输到本地文件。 刷新边栏选项卡时，从 Cloud Shell 中更新文件将反映在文件存储 GUI 中。

### <a name="download-files"></a>下载文件

![本地文件列表](media/download.png)
1. 在 Azure 门户中，转到已装载的文件共享。
2. 选择目标文件。
3. 选择“下载”按钮。

### <a name="upload-files"></a>上传文件

![要上传的本地文件](media/upload.png)
1. 请转到已装在的文件共享。
2. 选择“上传”按钮。
3. 选择要上传的文件。
4. 确认上传。

现在应可在 Cloud Shell 的 `clouddrive` 目录中查看可访问的文件。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) <br>
[了解 Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解存储标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

