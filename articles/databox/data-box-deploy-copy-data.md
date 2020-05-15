---
title: 有关通过 SMB 将数据复制到 Azure Data Box 的教程 | Microsoft Docs
description: 了解如何通过 SMB 将数据复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1730317296c672eb9347986ec41fdba25427408e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200489"
---
::: zone target="docs" 

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>教程：通过 SMB 将数据复制到 Azure Data Box

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>将数据复制到 Azure Data Box

::: zone-end

::: zone target="docs"

本教程介绍如何使用本地 Web UI 连接到主机并从中复制数据。

在本教程中，你将了解如何执行以下操作：

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
| Azure 块 Blob | <li>UNC 共享路径：`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 页 Blob  | <li>UNC 共享路径：`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 文件       |<li>UNC 共享路径：`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

如果使用 Windows Server 主机，请按照以下步骤连接到 Data Box。

1. 第一步是进行身份验证并启动会话。 转到“连接和复制”。  单击“获取凭据”，获取与存储帐户关联的共享的访问凭据。  

    ![获取共享凭据 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. 在“访问共享和复制数据”对话框中，复制对应于该共享的“用户名”和“密码”。   单击“确定”。 
    
    ![获取共享凭据 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. 若要从主机访问与存储帐户关联的共享（以下示例中为 devicemanagertest1），请打开命令窗口  。 在命令提示符处，键入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    根据数据格式，共享路径如下：
    - Azure 块 blob - `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure 页 blob - `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure 文件 - `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. 出现提示时，请输入共享的密码。 以下示例演示如何通过前面的命令连接到共享。

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. 按 Windows+R。在“运行”窗口中指定 `\\<device IP address>`。  单击“确定”打开文件资源管理器  。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    此时应能看到文件夹形式的共享。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹  。
    
如果使用 Linux 客户端，请使用以下命令装载 SMB 共享。 下面的“vers”参数是 Linux 主机支持的 SMB 版本。 在下面的命令中插入相应版本。 有关 Data Box 支持的 SMB 版本，请参阅 [Linux 客户端支持的文件系统](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`

## <a name="copy-data-to-data-box"></a>将数据复制到 Data Box

连接到 Data Box 共享后，下一步是复制数据。 在开始复制数据之前，请查看以下注意事项：

* 确保将数据复制到与适当数据格式对应的共享中。 例如，将块 Blob 数据复制到块 Blob 的共享中。 将 VHD 复制到页 Blob。 如果数据格式与相应的共享类型不匹配，则在后续步骤中，数据将无法上传到 Azure。
*  复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 限制](data-box-limits.md)中所述的大小限制。
* 如果 Data Box 正在上传的数据同时已由 Data Box 外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。
* 我们建议：
  * 不同时使用 SMB 和 NFS。
  * 将相同的数据复制到 Azure 上的同一个最终目标。

  在这些情况下，最终的结果不可确定。
* 始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹  。
* 请确保保留源数据的副本，直到可以确认 Data Box 已将数据传输到 Azure 存储中为止。

连接到 SMB 共享后，开始数据复制。 可以使用与 SMB 兼容的任何文件复制工具（例如 Robocopy）复制数据。 可以使用 Robocopy 启动多个复制作业。 使用以下命令：
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 下表描述了属性。
    
|Attribute  |说明  |
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
|log+:\<LogFile>| 将输出追加到现有的日志文件。|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 个 Robocopy 会话 <br> 每个会话 16 个线程    |    3 个 Robocopy 会话 <br> 每个会话 16 个线程    |    2 个 Robocopy 会话 <br> 每个会话 24 个线程    |


有关 Robocopy 命令的详细信息，请转到 [Robocopy 和几个示例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

打开目标文件夹，查看并验证复制的文件。 如果复制过程中遇到任何错误，请下载用于故障排除的错误文件。 有关详细信息，请参阅[查看将数据复制到 Data Box 期间的错误日志](data-box-logs.md#view-error-log-during-data-copy)。 有关数据复制期间的错误详细列表，请参阅 [Data Box 问题故障排除](data-box-troubleshoot.md)。

为确保数据完整性，复制数据时将以内联方式计算校验和。 复制完成后，检查设备上的已用空间和可用空间。
    
   ![在仪表板上检查可用空间和已用空间](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

可以通过 SMB、NFS、REST、数据复制服务将数据从源服务器复制到 Data Box 或托管磁盘。

在每种情况下，请确保共享和文件夹名称以及数据大小遵循 [Azure 存储和 Data Box 服务限制](data-box-limits.md)中所述的准则。

## <a name="copy-data-via-smb"></a>通过 SMB 复制数据

1. 如果使用 Windows 主机，请使用以下命令连接到 SMB 共享：

    `\\<IP address of your device>\ShareName`

2. 若要获取共享访问凭据，请在 Data Box 的本地 Web UI 中转到“连接和复制”页面。 
3. 使用与 SMB 兼容的文件复制工具（如 Robocopy）将数据复制到共享。 

有关分步说明，请转至[教程：通过 SMB 将数据复制到 Azure Data Box](data-box-deploy-copy-data.md)。

## <a name="copy-data-via-nfs"></a>通过 NFS 复制数据

1. 如果使用 NFS 主机，请使用以下命令在 Data Box 上装载 NFS 共享：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. 若要获取共享访问凭据，请在 Data Box 的本地 Web UI 中转到“连接和复制”页面。 
3. 使用 `cp` 或 `rsync` 命令复制数据。

有关分步说明，请转至[教程：通过 NFS 将数据复制到 Azure Data Box](data-box-deploy-copy-data-via-nfs.md)。

## <a name="copy-data-via-rest"></a>通过 REST 复制数据

1. 若要通过 REST API 使用 Data Box Blob 存储复制数据，可以通过 *http* 或 *https* 进行连接。
2. 若要将数据复制到 Data Box Blob 存储，可以使用 AzCopy。

有关分步说明，请转至[教程：通过 REST API 将数据复制到 Azure Data Box Blob 存储](data-box-deploy-copy-data-via-nfs.md)。

## <a name="copy-data-via-data-copy-service"></a>通过数据复制服务复制数据

1. 若要使用数据复制服务复制数据，需要创建一个作业。 在 Data Box 的本地 Web UI 中，转到“管理”>“复制数据”>“创建”。  
2. 填写参数并创建作业。

有关分步说明，请转至[教程：使用数据复制服务将数据复制到 Azure Data Box](data-box-deploy-copy-data-via-copy-service.md)。

## <a name="copy-data-to-managed-disks"></a>将数据复制到托管磁盘

1. 订购 Data Box 设备时，应该选择托管磁盘作为存储目标。
2. 可以通过 SMB 或 NFS 共享连接到 Data Box。
3. 然后，可以通过 SMB 或 NFS 工具复制数据。

有关分步说明，请转至[教程：在 Azure 中使用 Data Box 作为托管磁盘导入数据](data-box-deploy-copy-data-from-vhds.md)。

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 连接到 Data Box
> * 将数据复制到 Data Box


请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-picked-up.md)

::: zone-end

