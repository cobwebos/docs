---
title: 将数据复制到 Microsoft Azure Data Box 磁盘 | Microsoft Docs
description: 通过本教程了解如何将数据复制到 Azure Data Box 磁盘
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 418b158b127a688314fb3a0a506d116cc27da98c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678490"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>教程：将数据复制到 Azure Data Box Disk 并进行验证

本教程介绍如何从主机复制数据，然后生成校验和来验证数据完整性。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将数据复制到 Data Box 磁盘
> * 验证数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：
- 已完成[教程：安装和配置 Azure Data Box Disk](data-box-disk-deploy-set-up.md)。
- 磁盘已解锁，并且已连接到客户端计算机。
- 用来将数据复制到磁盘的客户端计算机必须运行[受支持的操作系统](data-box-disk-system-requirements.md##supported-operating-systems-for-clients)。
- 请确保数据的预期存储类型与[支持的存储类型](data-box-disk-system-requirements.md#supported-storage-types-for-upload)匹配。
- 查看 [Azure 对象大小限制中的托管磁盘限制](data-box-disk-limits.md#azure-object-size-limits)。


## <a name="copy-data-to-disks"></a>将数据复制到磁盘

在将数据复制到磁盘之前，请查看以下注意事项：

- 你需要负责确保将数据复制到与适当数据格式对应的文件夹中。 例如，将块 Blob 数据复制到块 Blob 的文件夹。 如果数据格式与相应的文件夹（存储类型）不匹配，则在后续步骤中，数据将无法上传到 Azure。
- 复制数据时，请确保数据大小符合 [Azure 存储和 Data Box 磁盘限制](data-box-disk-limits.md)中所述的大小限制。
- 如果 Data Box 磁盘正在上传的数据同时已由 Data Box 磁盘外部的其他应用程序上传，则可能会导致上传作业失败和数据损坏。

   > [!IMPORTANT]
   >  如果已在创建订单的过程中将托管磁盘指定为存储目标之一，那么以下部分就是适用的。

- 在所有预先创建的文件夹和所有 Data Box Disk 中，一个资源组只能包含一个具有给定名称的托管磁盘。 这意味着，上传到预先创建的文件夹的 VHD 应具有唯一的名称。 确保给定的名称与资源组中现有的托管磁盘不匹配。 如果 VHD 具有相同的名称，则只有一个 VHD 将转换为具有该名称的托管磁盘。 其他 VHD 作为页 blob 上传到临时存储帐户。
- 始终将 VHD 复制到某个预先创建的文件夹。 如果将 VHD 复制到这些文件夹以外或者复制到你自己创建的文件夹中，则 VHD 作为页 Blob 而不是托管磁盘上传到 Azure 存储帐户中。
- 只能上传固定的 VHD 来创建托管磁盘。 不支持动态 VHD、差异 VHD 或 VHDX 文件。


执行以下步骤，连接到计算机并将其上的数据复制到 Data Box 磁盘。

1. 查看已解锁的驱动器的内容。 根据放置 Data Box Disk 顺序时选择的选项，驱动器中预先创建的文件夹和子文件夹的列表会有所不同。

    |所选的存储目标  |存储帐户类型|临时存储帐户类型 |文件夹和子文件夹  |
    |---------|---------|---------|------------------|
    |存储帐户     |GPv1 或 GPv2                 | NA | BlockBlob <br> PageBlob <br> AzureFile        |
    |存储帐户     |Blob 存储帐户         | NA | BlockBlob        |
    |托管磁盘     |NA | GPv1 或 GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |存储帐户 <br> 托管磁盘     |GPv1 或 GPv2 | GPv1 或 GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |存储帐户 <br> 托管磁盘    |Blob 存储帐户 | GPv1 或 GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    下面显示了指定 GPv2 存储帐户的订单的示例屏幕截图：

    ![磁盘驱动器的内容](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. 将需要作为块 Blob 导入的数据复制到 BlockBlob 文件夹中。 同样，将 VHD/VHDX 等数据复制到 PageBlob 文件夹并将数据复制到 AzureFile 文件夹。

    在 Azure 存储帐户中，为 BlockBlob 和 PageBlob 文件夹下的每个子文件夹创建一个容器。 BlockBlob 和 PageBlob 文件夹下的所有文件将复制到 Azure 存储帐户下的默认容器 `$root` 中。 `$root` 容器中的所有文件始终作为块 Blob 上传。

   将文件复制到“AzureFile”文件夹中的文件夹。 AzureFile 文件夹中的子文件夹创建文件共享。 直接复制到 AzureFile 文件夹的文件都会失败，会作为块 Blob 上传。

    如果根目录中存在文件和文件夹，则必须先将它们移到另一个文件夹，然后开始复制数据。

    > [!IMPORTANT]
    > 所有容器、Blob 和文件名都应符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 如果不遵循这些规则，则无法将数据上传到 Azure。

3. 复制文件时，确保块 Blob 的文件不超过大约 4.7 TiB，页 Blob 的文件不超过大约 8 TiB，Azure 文件不超过大约 1 TiB。 
4. 可以使用文件资源管理器中的拖放操作复制数据。 也可以使用与 SMB 兼容的任何文件复制工具（例如 Robocopy）复制数据。 可以使用以下 Robocopy 命令启动多个复制作业：

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    下面以表格形式列出了命令的参数和选项：
    
    |参数/选项  |说明 |
    |--------------------|------------|
    |源            | 指定源目录的路径。        |
    |目标       | 指定目标目录的路径。        |
    |/E                  | 复制包括空目录的子目录。 |
    |/MT[:N]             | 使用 N 个线程创建多线程副本，其中 N 是介于 1 和 128 之间的整数。 <br>N 的默认值为 8。        |
    |/R:\<N>             | 指定复制失败时的重试次数。 N 的默认值为 1,000,000（100 万次重试）。        |
    |/W:\<N>             | 指定等待重试的间隔时间，以秒为单位。 N 的默认值为的 30（等待 30 秒）。        |
    |/NFL                | 指定不记录文件名。        |
    |/NDL                | 指定不记录目录名。        |
    |/FFT                | 采用 FAT 文件时间（精度为两秒）。        |
    |/Log:\<Log File>     | 将状态输出写入到日志文件（覆盖现有的日志文件）。         |

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
 
    若要优化性能，请在复制数据时使用以下 robocopy 参数。

    |    平台    |    大多为小于 512 KB 的小型文件                           |    大多为 512 KB-1 MB 的中型文件                      |    大多为 1 MB 以上的大型文件                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 个 Robocopy 会话* <br> 每个会话 16 个线程    |    2 个 Robocopy 会话* <br> 每个会话 16 个线程    |    2 个 Robocopy 会话* <br> 每个会话 16 个线程    |
    
    **每个 Robocopy 会话最多可包含 7,000 个目录和 1.5 亿个文件。*
    
    >[!NOTE]
    > 上面建议的参数基于内部测试中使用的环境。
    
    有关 Robocopy 命令的详细信息，请转到 [Robocopy 和几个示例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

6. 打开目标文件夹，查看并验证复制的文件。 如果复制过程中遇到任何错误，请下载用于故障排除的日志文件。 日志文件位于 robocopy 命令中指定的位置。
 
### <a name="split-and-copy-data-to-disks"></a>拆分数据并将其复制到磁盘

如果使用多个磁盘，并且需要拆分大型数据集并将其复制到所有磁盘中，则可以使用此可选过程。 借助 Data Box 拆分复制工具可以在 Windows 计算机上拆分和复制数据。

>[!IMPORTANT]
> Data Box 拆分复制工具还会验证数据。 如果使用 Data Box 拆分复制工具复制数据，则可以跳过[验证步骤](#validate-data)。
> 托管磁盘不支持拆分复制工具。

1. 在 Windows 计算机上，请确保将 Data Box 拆分复制工具下载并提取到某个本地文件夹中。 下载适用于 Windows 的 Data Box Disk 工具集时已下载此工具。
2. 打开文件资源管理器。 记下分配给 Data Box Disk 的数据源驱动器和驱动器号。 

     ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. 标识要复制的源数据。 例如，在本例中：
    - 标识了以下块 Blob 数据。

         ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - 标识了以下页 Blob 数据。

         ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. 转到该软件已提取到的文件夹。 在该文件夹中找到 `SampleConfig.json` 文件。 这是一个可以修改和保存的只读文件。

   ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. 修改 `SampleConfig.json` 文件。
 
   - 提供作业名称。 这会在 Data Box Disk 中创建一个文件夹，该文件夹最终将成为与这些磁盘关联的 Azure 存储帐户中的容器。 作业名称必须遵循 Azure 容器命名约定。 
   - 在 `SampleConfigFile.json` 中提供源路径并记下路径格式。 
   - 输入对应于目标磁盘的驱动器号。 数据取自源路径，并在多个磁盘之间复制。
   - 提供日志文件的路径。 默认情况下，日志文件将发送到 `.exe` 所在的当前目录中。

     ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. 若要验证文件格式，请转到 `JSONlint`。 将文件另存为 `ConfigFile.json`。 

     ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. 打开命令提示符窗口。 

8. 运行 `DataBoxDiskSplitCopy.exe`。 Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. 按 Enter 继续运行脚本。

    ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. 拆分并复制数据集后，会显示拆分复制工具的复制会话摘要。 下面显示了示例输出。

    ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. 验证是否在目标磁盘之间拆分了数据。 
 
    ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    如果进一步检查 `n:` 驱动器的内容，将会看到已创建了对应于块 Blob 和页 Blob 格式数据的两个子文件夹。
    
     ![拆分复制数据](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. 如果复制会话失败，可使用以下命令予以恢复：

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

数据复制完成后，可以继续验证数据。 如果使用了拆分复制工具，请跳过验证（拆分复制工具也会验证）并继续学习下一教程。


## <a name="validate-data"></a>验证数据

如果未使用拆分复制工具复制数据，则需要验证数据。 若要验证数据，请执行以下步骤。

1. 运行 `DataBoxDiskValidation.cmd` 以在驱动器的 *DataBoxDiskImport* 文件夹中进行校验和验证。
    
    ![Data Box Disk 验证工具输出](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. 选择合适的选项。 **建议你始终选择选项 2 来验证文件并生成校验和**。 根据具体的数据大小，此步骤可能需要一段时间。 在脚本完成后，退出命令窗口。 如果在验证和校验和生成过程中出现任何错误，则会向你发送通知并提供指向错误日志的链接。

    ![校验和输出](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - 在两次运行之间请重置工具。
    > - 如果要处理包含小文件（数 KB）的大型数据集，请使用选项 1。 此选项仅验证文件，因为校验和生成可能需要很长时间，并且执行速度可能会非常慢。

3. 如果使用了多个磁盘，请对每个磁盘运行该命令。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 将数据复制到 Data Box 磁盘
> * 验证数据完整性

请继续学习下一篇教程，了解如何退回 Data Box 磁盘和验证向 Azure 上传数据的结果。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄回到 Microsoft](./data-box-disk-deploy-picked-up.md)
