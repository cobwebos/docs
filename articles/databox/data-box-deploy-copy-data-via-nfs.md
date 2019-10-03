---
title: 有关通过 NFS 将数据复制到 Azure Data Box 的教程| Microsoft Docs
description: 了解如何通过 NFS 将数据复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: c74ed93383ea880900a5428a6f24b5b44a3ff135
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443151"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>教程：通过 NFS 将数据复制到 Azure Data Box

本教程介绍如何使用本地 Web UI 连接到主机并从中复制数据。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 先决条件
> * 连接到 Data Box
> * 将数据复制到 Data Box

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：设置 Azure Data Box](data-box-deploy-set-up.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。 
3. 你有一台主机，其中的数据需复制到 Data Box。 该主机必须
    - 运行[支持的操作系统](data-box-system-requirements.md)。
    - 连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路，但复制速度会受影响。 

## <a name="connect-to-data-box"></a>连接到 Data Box

根据选择的存储帐户，Data Box 将会：
- 为每个关联的 GPv1 和 GPv2 存储帐户最多创建三个共享。
- 用于高级存储的一个共享。 
- 用于 Blob 存储帐户的一个共享。 

在块 blob 和页 blob 共享下，一级实体为容器，二级实体为 blob。 在 Azure 文件共享下，一级实体为共享，二级实体为文件。

下表显示了 Data Box 上共享的 UNC 路径以及上传数据的 Azure 存储路径 URL。 最终的 Azure 存储路径 URL 可以从 UNC 共享路径派生。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 块 Blob | <li>UNC 共享路径：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 页 Blob  | <li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 文件       |<li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

如果使用 Linux 主机，请执行以下步骤将 Data Box 配置为允许 NFS 客户端访问。

1. 提供允许访问共享的客户端的 IP 地址。 在本地 Web UI 中，转到“连接和复制”页。  在“NFS 设置”下，单击“NFS 客户端访问”。   

    ![配置 NFS 客户端访问 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. 提供 NFS 客户端的 IP 地址，然后单击“添加”。  可以重复此步骤为多个 NFS 客户端配置访问。 单击“确定”。 

    ![配置 NFS 客户端访问 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. 确保 Linux 主机上已安装[受支持版本](data-box-system-requirements.md)的 NFS 客户端。 使用特定版本的 Linux 分发版。 

3. 安装 NFS 客户端后，使用以下命令在 Data Box 设备上装载 NFS 共享：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    以下示例演示如何通过 NFS 连接到 Data Box 共享。 Data Box 设备 IP 为 `10.161.23.130`，共享 `Mystoracct_Blob` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    对于 Mac 客户端，需要添加一个附加选项，如下所示： 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹  。

## <a name="copy-data-to-data-box"></a>将数据复制到 Data Box

连接到 Data Box 共享后，下一步是复制数据。 在开始复制数据之前，请查看以下注意事项：

- 确保将数据复制到与适当数据格式对应的共享中。 例如，将块 Blob 数据复制到块 Blob 的共享中。 将 VHD 复制到页 Blob。 如果数据格式与相应的共享类型不匹配，则在后续步骤中，数据将无法上传到 Azure。
-  复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 限制](data-box-limits.md)中所述的大小限制。 
- 如果 Data Box 正在上传的数据同时已由 Data Box 外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。
- 我们建议不要同时使用 SMB 和 NFS，也不要将相同的数据复制到 Azure 上的同一个最终目标。 在这种情况下，最终的结果不可确定。
- **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹  。
- 如果从 NFS 共享中将区分大小写的目录和文件名引入到 Data Box 上的 NFS： 
    - 名称将保留大小写。
    - 文件不区分大小写。
    
    例如，如果复制 `SampleFile.txt` 和 `Samplefile.Txt`，则在复制到 Data Box 时，名称将保留大小写，但第二个文件将覆盖第一个文件，因为这些文件被视为同一文件。


如果使用 Linux 主机，请使用类似于 Robocopy 的复制实用工具。 在 Linux 中可用的一些替代工具包括 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。  

`cp` 命令是用于复制目录的最佳选项之一。 有关用法详细信息，请转到 [cp 手册页](http://man7.org/linux/man-pages/man1/cp.1.html)。

如果使用 rsync 选项进行多线程复制，请遵循以下准则：

 - 根据 Linux 客户端所用的文件系统，安装 **CIFS Utils** 或 **NFS Utils** 包。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  安装 **Rsync** 和 **Parallel**（根据 Linux 分发版而异）。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - 创建装入点。

    `sudo mkdir /mnt/databox`

 - 装载卷。

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - 镜像文件夹目录结构。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - 复制文件。 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     其中，j 指定并行化数目，X 为并行副本数

     我们建议从 16 个并行副本开始，并根据可用的资源增加线程数。

> [!IMPORTANT]
> 不支持以下 Linux 文件类型：符号链接、字符文件、块文件、套接字和管道。 在**准备交付**步骤期间，这些文件类型将导致失败。

打开目标文件夹，查看并验证复制的文件。 如果复制过程中遇到任何错误，请下载用于故障排除的错误文件。 有关详细信息，请参阅[查看将数据复制到 Data Box 期间的错误日志](data-box-logs.md#view-error-log-during-data-copy)。 有关数据复制期间的错误详细列表，请参阅 [Data Box 问题故障排除](data-box-troubleshoot.md)。

为确保数据完整性，复制数据时将以内联方式计算校验和。 复制完成后，检查设备上的已用空间和可用空间。
    
   ![在仪表板上检查可用空间和已用空间](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 连接到 Data Box
> * 将数据复制到 Data Box


请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-picked-up.md)

