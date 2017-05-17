---
title: "在 Azure Cloud Shell（预览版）中持久保存文件 | Microsoft Docs"
description: "演练 Azure Cloud Shell 如何持久保存文件。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 9093bf30a67fd762b237faa61071137fd83d048f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中持久保存文件
初始启动时，Azure Cloud Shell 会要求你提供订阅，以便为你创建 LRS 存储帐户和 Azure 文件共享。

![](media/storage-prompt.png)

### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>它将替你在离你最近的受支持区域中创建三个资源：
1. 资源组：`cloud-shell-storage-<region>`
2. 存储帐户：`cs-uniqueGuid`
3. 文件共享：`cs-<user>-<domain>-com-uniqueGuid`

此文件共享将装载为 $Home 目录下的 `clouddrive`。 此文件共享还用来存储所创建的一个 5-GB 映像，该映像自动更新并持久保存 $Home 目录。 这是一个一次性操作，对于后续会话会自动装载。

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell 通过以下两种方法来持久保存文件：
1. 创建 $Home 目录的磁盘映像来持久保存 $Home 中的文件。 此磁盘映像将作为 `<User>.img` 保存在你指定的文件共享中，位于以下位置：`fileshare.storage.windows.net/fileshare/.cloudconsole/<User>.img`

2. 将指定的文件共享装载为 $Home 目录中的 `clouddrive` 以便直接进行文件共享交互。 
`/Home/<User>/clouddrive` 映射到 `fileshare.storage.windows.net/fileshare`。
 
## <a name="using-clouddrive"></a>使用 clouddrive
Cloud Shell 允许用户运行名为 `clouddrive` 的命令，该命令可用于手动更新装载到 Cloud Shell 的文件共享。
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>装载新的 clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>手动装载的先决条件
首次启动时，Cloud Shell 会为你创建一个存储帐户和文件共享，不过你可以使用 `clouddrive mount` 命令更新该文件共享。

如果装载现有的文件共享，则存储帐户必须：
1. 是 LRS 或 GRS 才能支持文件共享。
2. 位于为你分配的区域中。 在装入时，你被分配到的区域将列在资源组名称 `cloud-shell-storage-<region>` 中。

### <a name="supported-storage-regions"></a>支持的存储区域
Azure 文件必须与所装载到的计算机位于同一区域中。 Cloud Shell 计算机位于以下区域中：
|区域|区域|
|---|---|
|美洲|美国东部、美国中南部、美国西部|
|欧洲|欧洲北部、欧洲西部|
|亚太区|印度中部、亚洲东南部|

### <a name="mount-command"></a>装载命令

> [!NOTE]
> 如果装载新的文件共享，将会为你的 $Home 目录创建一个新的用户映像，因为你以前的 $Home 映像保存在以前的文件共享中。

1. 使用以下参数运行 `clouddrive mount` <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要查看更多详细信息，请运行 `clouddrive mount -h`： <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>卸载 clouddrive
你随时可以卸载已装载到 Cloud Shell 的文件共享。 不过，Cloud Shell 需要一个已装载的文件共享，因此，如果删除了已装载的文件共享，则在下次会话时会提示你创建并装载一个新的文件共享。

若要从 Cloud Shell 分离文件共享，请执行以下操作：
1. 运行 `clouddrive unmount`
2. 承认并确认提示

除非手动删除，否则你的文件共享将一直存在。 Cloud Shell 在后续会话中不会再搜索此文件共享。

若要查看更多详细信息，请运行 `clouddrive mount -h`： <br>
![](media/unmount-h.png)

> [!WARNING]
> 此命令不会删除任何资源。 不过，手动删除映射到 Cloud Shell 的资源组、存储帐户或文件共享将会清除你的 $Home 目录磁盘映像以及你的文件共享中的任何文件。 这不可撤消。

## <a name="list-clouddrive"></a>列出 clouddrive
若要查明哪些文件共享已装载为 `clouddrive`，请执行以下操作：
1. 运行 `df` 

clouddrive 的文件路径将在 url 中显示存储帐户名称和文件共享。

`//storageaccountname.file.core.windows.net/filesharename`

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

## <a name="upload-or-download-local-files"></a>上传或下载本地文件
可以使用 Azure 门户来管理本地文件在文件共享中的传入或传出。
从 Cloud Shell 中更新文件的结果将在刷新边栏选项卡时反映在文件存储 GUI 中。

1. 导航到已装载的文件共享 ![](media/touch-txt-storage.png)
2. 在门户中选择目标文件
3. 点击“下载”![](media/download-storage.png)

如果需要下载存在于 `clouddrive` 外部的文件，请执行以下操作：
1. 将文件复制到 `/<User>/clouddrive` <br>
2. 执行[前面的步骤](#upload-or-download-local-files) <br>

## <a name="cloud-shell-tagging"></a>Cloud Shell 标记
Cloud Shell 会使用以下格式向已装载的存储帐户添加一个“标记”： <br>

| 键 | 值 |
|:-------------:|:-------------:|
|cloud-console-files-for-user@domain.com|fileshareName|

可以使用这些标记来了解哪些用户映射到了特定的文件共享以及可以在何处找到特定的 $Home 映像。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) 
[了解 Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 
[了解存储标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 
