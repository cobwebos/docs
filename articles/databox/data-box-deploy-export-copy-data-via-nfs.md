---
title: 有关通过 NFS 从 Azure Data Box 复制数据的教程 | Microsoft Docs
description: 了解如何通过 NFS 从 Azure Data Box 复制数据
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: alkohli
ms.openlocfilehash: bd8e6d4175c57bd31c3fd83bf6f9669d2b65ffb2
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660831"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>教程：通过 NFS 从 Azure Data Box 复制数据（预览版）

本教程介绍如何通过 NFS 连接到 Data Box 的本地 Web UI 并从中将数据复制到本地数据服务器。 Data Box 上的数据是从 Azure 存储帐户导出的。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到 Data Box
> * 从 Data Box 复制数据

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你已订购 Azure Data Box。
    - 有关导入订单，请参阅[教程：订购 Azure Data Box](data-box-deploy-ordered.md)。
    - 有关导出订单，请参阅[教程：订购 Azure Data Box](data-box-deploy-export-ordered.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。
3. 你有一台主机，需要将数据从 Data Box 复制到其中。 该主机必须
   * 运行[支持的操作系统](data-box-system-requirements.md)。
   * 连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路，但复制速度会受影响。

## <a name="connect-to-data-box"></a>连接到 Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

如果使用 Linux 主机，请执行以下步骤将 Data Box 配置为允许 NFS 客户端访问。 Data Box 一次最多可以连接五个 NFS 客户端。

1. 提供允许访问共享的客户端的 IP 地址：

    1.  在本地 Web UI 中，转到“连接和复制”页。 在“NFS 设置”下，单击“NFS 客户端访问”。  

        ![打开 NFS 客户端访问](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. 若要添加 NFS 客户端，请提供客户端的 IP 地址，并单击“添加”。 Data Box 一次最多可以连接五个 NFS 客户端。 完成后，单击“确定”。

         ![添加 NFS 客户端](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. 确保 Linux 主机上已安装[受支持版本](data-box-system-requirements.md)的 NFS 客户端。 使用特定版本的 Linux 分发版。 

3. 安装 NFS 客户端后，使用以下命令在 Data Box 设备上装载 NFS 共享：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    以下示例演示如何通过 NFS 连接到 Data Box 共享。 Data Box 设备 IP 为 `10.161.23.130`，共享 `Mystoracct_Blob` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    对于 Mac 客户端，需要添加一个附加选项，如下所示： 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹。

## <a name="copy-data-from-data-box"></a>从 Data Box 复制数据

连接到 Data Box 共享后，下一步是复制数据。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 现在即可开始复制数据。 如果使用 Linux 主机，请使用类似于 Robocopy 的复制实用工具。 在 Linux 中可用的一些替代工具包括 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。  

`cp` 命令是用于复制目录的最佳选项之一。 有关用法详细信息，请转到 [cp 手册页](http://man7.org/linux/man-pages/man1/cp.1.html)。

如果使用 rsync 选项进行多线程复制，请遵循以下准则：

* 根据 Linux 客户端所用的文件系统，安装 **CIFS Utils** 或 **NFS Utils** 包。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* 安装“Rsync”和“Parallel”（根据 Linux 分发版而异） 。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* 创建装入点。

    `sudo mkdir /mnt/databox`

* 装载卷。

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* 镜像文件夹目录结构。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* 复制文件。

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     其中，j 指定并行化数目，X 为并行副本数

     我们建议从 16 个并行副本开始，并根据可用的资源增加线程数。

> [!IMPORTANT]
> 不支持以下 Linux 文件类型：符号链接、字符文件、块文件、套接字和管道。 在**准备交付**步骤期间，这些文件类型将导致失败。

复制完成后，转到“仪表板”，检查设备上的已用空间和可用空间。

现在，你可以继续将 Data Box 交付到 Microsoft。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到 Data Box
> * 从 Data Box 复制数据

请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-export-picked-up.md)
