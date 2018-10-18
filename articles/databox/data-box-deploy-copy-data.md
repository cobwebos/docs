---
title: 将数据复制到 Microsoft Azure Data Box | Microsoft Docs
description: 了解如何将数据复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: b59830677ac8c07c6b7adbab24c82ca25d71f5a0
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093453"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>教程：将数据复制到 Azure Data Box 

本教程介绍如何使用本地 Web UI 连接到主机并从中复制数据，然后准备交付 Data Box。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 连接到 Data Box
> * 将数据复制到 Data Box
> * 准备交付 Data Box。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：设置 Azure Data Box](data-box-deploy-set-up.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。
3. 你有一台主机，其中的数据需复制到 Data Box。 该主机必须
    - 运行[支持的操作系统](data-box-system-requirements.md)。
    - 连接到高速网络。 强烈建议至少建立一个 10 GbE 连接。 如果 10 GbE 连接不可用，可以使用 1 GbE 数据链路，但复制速度会受影响。 

## <a name="connect-to-data-box"></a>连接到 Data Box

根据选择的存储帐户，Data Box 将会：
- 为每个关联的 GPv1 和 GPv2 存储帐户最多创建三个共享。
- 为高级或 Blob 存储帐户创建一个共享。 

块 Blob 和页 Blob 共享下的一级实体为容器，二级实体为 Blob。 在 Azure 文件共享下，一级实体为共享，二级实体为文件。

请考虑以下示例。 

- 存储帐户：*Mystoracct*
- 块 Blob 的共享：*Mystoracct_BlockBlob/my-container/blob*
- 页 Blob 的共享：*Mystoracct_PageBlob/my-container/blob*
- 文件的共享：*Mystoracct_AzFile/my-share*

根据 Data Box 是已连接到 Windows Server 主机还是 Linux 主机，连接和复制的步骤可能有所不同。

### <a name="connect-via-smb"></a>通过 SMB 连接 

如果使用 Windows Server 主机，请执行以下步骤连接到 Data Box。

1. 第一步是进行身份验证并启动会话。 转到“连接和复制”。 单击“获取凭据”，获取与存储帐户关联的共享的访问凭据。 

    ![获取共享凭据 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. 在“访问共享和复制数据”对话框中，复制对应于该共享的“用户名”和“密码”。 单击“确定”。
    
    ![获取共享凭据 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. 访问与存储帐户（在以下示例中为 Mystoracct）关联的共享。 使用 `\\<IP of the device>\ShareName` 路径访问共享。 根据数据格式，连接到位于以下地址的共享（使用共享名称）： 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    若要从主机连接到共享，请打开命令窗口。 在命令提示符处，键入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    出现提示时，请输入共享的密码。 以下示例演示如何通过前面的命令连接到共享。

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. 按 Windows+R。在“运行”窗口中指定 `\\<device IP address>`。 单击“确定”。 此时会打开文件资源管理器。 此时应能看到文件夹形式的共享。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5.  **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 有时，文件夹可能显示一个灰色的叉形符号。 此符号不代表出错。 应用程序通过标记文件夹来跟踪状态。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>通过 NFS 连接 

如果使用 Linux 主机，请执行以下步骤将 Data Box 配置为允许 NFS 客户端访问。

1. 提供允许访问共享的客户端的 IP 地址。 在本地 Web UI 中，转到“连接和复制”页。 在“NFS 设置”下，单击“NFS 客户端访问”。 

    ![配置 NFS 客户端访问 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. 提供 NFS 客户端的 IP 地址，然后单击“添加”。 可以重复此步骤为多个 NFS 客户端配置访问。 单击“确定”。

    ![配置 NFS 客户端访问 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. 确保 Linux 主机上已安装[受支持版本](data-box-system-requirements.md)的 NFS 客户端。 使用特定版本的 Linux 分发版。 

3. 安装 NFS 客户端后，使用以下命令在 Data Box 设备上装载 NFS 共享：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    以下示例演示如何通过 NFS 连接到 Data Box 共享。 Data Box 设备 IP 为 `10.161.23.130`，共享 `Mystoracct_Blob` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>将数据复制到 Data Box

连接到 Data Box 共享后，下一步是复制数据。 在复制数据之前，请务必查看以下注意事项：

- 确保将数据复制到与适当数据格式对应的共享中。 例如，将块 Blob 数据复制到块 Blob 的共享中。 如果数据格式与相应的共享类型不匹配，则在后续步骤中，数据将无法上传到 Azure。
-  复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 限制](data-box-limits.md)中所述的大小限制。 
- 如果 Data Box 正在上传的数据同时已由 Data Box 外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。
- 我们建议不要同时使用 SMB 和 NFS，也不要将相同的数据复制到 Azure 上的同一个最终目标。 在这种情况下，最终的结果不可确定。

### <a name="copy-data-via-smb"></a>通过 SMB 复制数据

连接到 SMB 共享后，启动数据复制。 

可以使用与 SMB 兼容的任何文件复制工具（例如 Robocopy）复制数据。 可以使用 Robocopy 启动多个复制作业。 请使用以下命令：
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 下表描述了属性。
    
|属性  |Description  |
|---------|---------|
|/e     |复制包括空目录的子目录。         |
|/r:     |指定复制失败时的重试次数。         |
|/w:     |指定等待重试的间隔时间，以秒为单位。         |
|/is     |包括相同的文件。         |
|/nfl     |指定不记录文件名。         |
|/ndl    |指定不记录目录名。        |
|/np     |指定不要显示复制操作的进度（到目前为止复制的文件或目录数）。 显示进度会明显降低性能。         |
|/MT     | 使用多线程，建议使用 32 或 64 个线程。 不要对加密的文件使用此选项。 可能需要将加密和未加密的文件隔离开来。 但是，单线程复制会明显降低性能。           |
|/fft     | 用于减少任何文件系统的时间戳粒度。        |
|/b     | 在备份模式下复制文件。        |
|/z    | 在重启模式下复制文件，如果环境不稳定，请使用此选项。 由于日志记录量增加，此选项会降低吞吐量。      |
| /zb     | 使用重启模式。 如果访问被拒绝，此选项将使用备份模式。 由于要设置检查点，此选项会降低吞吐量。         |
|/efsraw     | 在 EFS 原始模式下复制所有加密的文件。 请仅对加密的文件使用此选项。         |
|log+:<LogFile>| 将输出追加到现有的日志文件。|    
 
以下示例演示用于将文件复制到 Data Box 的 robocopy 命令的输出。
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

若要优化性能，请在复制数据时使用以下 robocopy 参数。

|    平台    |    大多为小于 512 KB 的小型文件                           |    大多为 512 KB-1 MB 的中型文件                      |    大多为 1 MB 以上的大型文件                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 个 Robocopy 会话 <br> 每个会话 16 个线程    |    3 个 Robocopy 会话 <br> 每个会话 16 个线程    |    2 个 Robocopy 会话 <br> 每个会话 24 个线程    |  |


有关 Robocopy 命令的详细信息，请转到 [Robocopy 和几个示例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

打开目标文件夹，查看并验证复制的文件。 如果复制过程中遇到任何错误，请下载用于故障排除的错误文件。

为确保数据完整性，复制数据时将以内联方式计算校验和。 复制完成后，检查设备上的已用空间和可用空间。
    
   ![在仪表板上检查可用空间和已用空间](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>通过 NFS 复制数据

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

## <a name="prepare-to-ship"></a>准备交付

最后一步是准备好要交付的设备。 在此步骤中，所有设备共享将会脱机。 一旦开始准备要交付的设备，就无法访问共享。
1. 转到“准备交付”并单击“开始准备”。 
   
    ![准备交付 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. 默认情况下，校验和是在准备交付期间以内联方式计算的。 校验和计算可能需要一些时间，具体取决于数据的大小。 单击“开始准备”。
    1. 在准备交付时，设备共享将会脱机，设备将会锁定。
        
        ![准备交付 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. 设备准备完成后，设备状态将更新为“交付准备就绪”。 
        
        ![准备交付 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. 下载在此过程中复制的文件的列表（清单）。 以后可以使用此列表来确认文件是否已上传到 Azure。
        
        ![准备交付 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. 关闭设备。 转到“关闭或重启”页，单击“关闭”。 出现确认提示时，请单击“确定”以继续。
4. 拔下电缆。 下一步是将设备寄送到 Microsoft。

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 连接到 Data Box
> * 将数据复制到 Data Box
> * 准备交付 Data Box

请继续学习下一篇教程，了解如何设置和复制 Data Box 上的数据。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-picked-up.md)

