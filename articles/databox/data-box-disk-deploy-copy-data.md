---
title: 将数据复制到 Microsoft Azure Data Box 磁盘 | Microsoft Docs
description: 通过本教程了解如何将数据复制到 Azure Data Box 磁盘
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010814"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>教程：将数据复制到 Azure Data Box 磁盘并验证

本教程介绍如何从主机复制数据，然后生成校验和来验证数据完整性。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 将数据复制到 Data Box 磁盘
> * 验证数据完整性

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：
- 已完成[教程：安装和配置 Azure Data Box 磁盘](data-box-disk-deploy-set-up.md)。
- 磁盘已拆包并已通电。
- 有一台要将数据复制到磁盘的主机。 该主机必须
    - 运行[支持的操作系统](data-box-disk-system-requirements.md)。
    - [装有 Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855)。
    - [装有 .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)。


## <a name="copy-data-to-disks"></a>将数据复制到磁盘

执行以下步骤，连接到计算机并将其上的数据复制到 Data Box 磁盘。

1. 查看已解锁的驱动器的内容。 

    ![查看驱动器内容](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. 将需要作为块 Blob 导入的数据复制到 BlockBlob 文件夹中。 同理，将 VHD/VHDX 等数据复制到 PageBlob 文件夹中。 

    在 Azure 存储帐户中，为 BlockBlob 和 PageBlob 文件夹下的每个子文件夹创建一个容器。 BlockBlob 和 PageBlob 文件夹下的所有文件将复制到 Azure 存储帐户下的默认容器 `$root` 中。 `$root` 容器中的所有文件始终作为块 Blob 上传。

    如果根目录中存在文件和文件夹，则必须先将它们移到另一个文件夹，然后开始复制数据。

    在容器和 Blob 名称方面遵循 Azure 命名要求。

    |实体   |约定  |
    |---------|---------|
    |容器名称：块 Blob 和页 Blob     |必须以字母或数字开头，只能包含小写字母、数字和连字符 (-)。 每个连字符 (-) 字符的前后必须紧接字母或数字。 名称中不允许连续的连字符。 <br>必须是有效的 DNS 名称，长度为 3 到 63 个字符。          |
    |块 Blob 和页 Blob 的 Blob 名称    |Blob 名称区分大小写，只能包含字符的任意组合。 <br>Blob 名称的长度必须为 1 到 1,024 个字符。<br>必须正确转义保留的 URL 字符。<br>构成 Blob 名称的路径段数目不能超过 254 个。 路径段是指对应于虚拟目录名称的相邻分隔符（例如，正斜杠“/”）之间的字符串。         |

    > [!IMPORTANT] 
    > 所有容器和 Blob 应符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions)。 如果不遵循这些规则，则无法将数据上传到 Azure。

3. 复制文件时，请确保块 Blob 的文件不超过大约 4.7 TiB，页 Blob 的文件不超过大约 8 TiB。 
4. 可以使用文件资源管理器中的拖放操作复制数据。 也可以使用与 SMB 兼容的任何文件复制工具（例如 Robocopy）复制数据。 可以使用以下 Robocopy 命令启动多个复制作业：

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    下面以表格形式列出了命令的参数和选项：
    
    |参数/选项  |Description |
    |--------------------|------------|
    |<Source>            | 指定源目录的路径。        |
    |<Destination>       | 指定目标目录的路径。        |
    |/E                  | 复制包括空目录的子目录。 |
    |/MT[:N]             | 使用 N 个线程创建多线程副本，其中 N 是介于 1 和 128 之间的整数。 <br>N 的默认值为 8。        |
    |/R: <N>             | 指定复制失败时的重试次数。 N 的默认值为 1,000,000（100 万次重试）。        |
    |/W: <N>             | 指定等待重试的间隔时间，以秒为单位。 N 的默认值为的 30（等待 30 秒）。        |
    |/NFL                | 指定不记录文件名。        |
    |/NDL                | 指定不记录目录名。        |
    |/FFT                | 采用 FAT 文件时间（精度为两秒）。        |
    |/Log:<Log File>     | 将状态输出写入到日志文件（覆盖现有的日志文件）。         |

    可以配合每个磁盘上运行的多个作业一起使用多个磁盘。 

6. 当作业正在进行时检查复制状态。 以下示例显示了将文件复制到 Data Box 磁盘的 robocopy 命令的输出。

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. 打开目标文件夹，查看并验证复制的文件。 如果复制过程中遇到任何错误，请下载用于故障排除的日志文件。 日志文件位于 robobopy 命令中指定的位置。
 


> [!IMPORTANT]
> - 你需要负责确保将数据复制到与适当数据格式对应的文件夹中。 例如，将块 Blob 数据复制到块 Blob 的文件夹。 如果数据格式与相应的文件夹（存储类型）不匹配，则在后续步骤中，数据将无法上传到 Azure。
> -  复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 磁盘限制](data-box-disk-limits.md)中所述的大小限制。 
> - 如果 Data Box 磁盘正在上传的数据同时已由 Data Box 磁盘外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。

## <a name="verify-data-integrity"></a>验证数据完整性

若要验证数据完整性，请执行以下步骤。

1. 运行 `AzureExpressDiskService.ps1` 执行校验和验证。 在文件资源管理器中，转到驱动器的 *AzureImportExport* 文件夹。 单击右键并选择“使用 PowerShell 运行”。 

    ![运行校验和](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. 根据具体的数据大小，此步骤可能需要一段时间。 脚本完成后，将显示数据完整性检查过程的摘要，以及完成该过程所花费的时间。 可以按 **Enter** 退出命令窗口。

    ![校验和输出](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. 如果使用了多个磁盘，请对每个磁盘运行该命令。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 将数据复制到 Data Box 磁盘
> * 验证数据完整性

请继续学习下一篇教程，了解如何退回 Data Box 磁盘和验证向 Azure 上传数据的结果。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄回到 Microsoft](./data-box-disk-deploy-picked-up.md)

