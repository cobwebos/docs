---
title: 如何部署 Azure 文件 | Microsoft Docs
description: 了解如何从头至尾部署 Azure 文件。
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.openlocfilehash: 4cced4a735aaf92f803b45fab4afe5102020d469
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144265"
---
# <a name="how-to-deploy-azure-files"></a>如何部署 Azure 文件

  [Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 本文介绍如何在组织内实际部署 Azure 文件。

强烈建议在按照本文中的步骤操作之前，阅读[规划 Azure 文件部署](storage-files-planning.md)。

## <a name="prerequisites"></a>先决条件
本文假设你已完成下列步骤：

- 在所需区域创建了具有所需复原和加密选项的 Azure 存储帐户。 有关如何创建存储帐户的分步说明，请参阅[创建存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
- 在存储帐户中创建了具有所需配额的 Azure 文件共享。 有关如何创建文件共享的分步说明，请参阅[创建文件共享](storage-how-to-create-file-share.md)。

## <a name="transfer-data-into-azure-files"></a>将数据传输到 Azure 文件
可能需要将现有文件共享（例如本地存储的文件共享）迁移到新的 Azure 文件共享。 本部分介绍如何通过[规划指南](storage-files-planning.md#data-transfer-method)中详述的几种常用方法将数据移动到 Azure 文件共享

### <a name="azure-file-sync"></a>Azure 文件同步
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 你可以使用 Windows Server 上的任意可用协议在本地访问数据（包括 SMB、NFS 和 FTPS），并且可以在世界各地获取所需的缓存数。

Azure 文件同步也可用于将数据迁移到 Azure 文件共享，即使长期使用不需要此同步机制。 若要深入了解如何使用 Azure 文件同步将数据传输到 Azure 文件共享，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

### <a name="azure-importexport"></a>Azure 导入/导出
使用 Azure 导入/导出服务，可将硬盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure 文件共享。 有关此服务的更详细概述，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Azure 存储](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

> [!Note]  
> Azure 导入/导出服务目前不支持从 Azure 文件共享导出文件。

执行以下步骤可将数据从本地位置导入 Azure 文件共享。

1. 获取所需数量的硬盘，将其邮寄到 Azure。 硬盘可以是任何磁盘大小，但必须是支持 SATA II 或 SATA III 标准的 2.5 英寸或 3.5 英寸 SSD 或 HDD。 

2. 在执行数据传输的服务器/电脑上连接并装载每个磁盘。 为了获得最佳性能，建议在包含该数据的服务器上本地运行本地导出作业。 在某些情况下，例如提供数据的文件服务器是 NAS 设备时，可能无法实现这一点。 在这种情况下，完全可以在电脑上装载所有磁盘。

3. 确保每个驱动器处于联机状态、已初始化，并分配有驱动器号。 要对驱动器执行联机、初始化和分配驱动器号等操作，请打开磁盘管理 MMC 管理单元 (diskmgmt.msc)。

    - 要使磁盘联机（如尚未联机），请在磁盘管理 MMC 的下方窗格中右键单击磁盘，然后选择“联机”。
    - 要初始化磁盘，请在下方窗格中右键单击磁盘（磁盘已联机），然后选择“初始化”。 请务必在出现提示时选择“GPT”。

        ![磁盘管理 MMC 中“初始化磁盘”菜单的屏幕截图](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - 要为磁盘分配驱动器号，请右键单击处于联机状态且已初始化的磁盘的“未分配”空间，然后单击“新建简单卷”。 可据此分配驱动器号。 请注意，不需要格式化卷，因为稍后会执行此操作。

        ![磁盘管理 MMC 中“新建简单卷”向导的屏幕截图](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. 创建数据集 CSV 文件。 数据集 CSV 文件是本地数据路径与数据应复制到的所需 Azure 文件共享之间的映射。 例如，以下数据集 CSV 文件将本地文件共享（“F:\shares\scratch”）映射到 Azure 文件共享（“MyAzureFileShare”）：
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    可以指定同一存储帐户的多个共享。 有关详细信息，请参阅[准备数据集 CSV 文件](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file)。

5. 创建驱动器集 CSV 文件。 驱动器集 CSV 文件可列出本地导出代理可用的磁盘。 例如，以下驱动器集 CSV 文件可列出用于本地导出作业的 `X:`、`Y:` 和 `Z:` 驱动器：

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    有关详细信息，请参阅[准备驱动器集 CSV 文件](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file)。

6. 使用 [WAImportExport 工具](https://www.microsoft.com/en-us/download/details.aspx?id=55280)将数据复制到一个或多个硬盘驱动器。

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > 完成磁盘准备操作以后，请勿修改硬盘驱动器上的数据，也勿修改日志文件。

7. [创建导入作业](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job)。
    
### <a name="robocopy"></a>Robocopy
Robocopy 是 Windows 和 Windows Server 自带的一款知名复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。 Robocopy 操作非常简单：

1. [装载 Azure 文件共享](storage-how-to-use-files-windows.md)。 为了获得最佳性能，建议在包含该数据的服务器上本地装载 Azure 文件共享。 在某些情况下，例如提供数据的文件服务器是 NAS 设备时，可能无法实现这一点。 在这种情况下，完全可以在电脑上装载 Azure 文件共享。 本示例在命令行处使用 `net use` 来装载文件共享：

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. 在命令行处使用 `robocopy` 将数据移动到 Azure 文件共享：

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy 提供丰富的选项，用户可根据需要修改复制行为。 有关详细信息，请查看 [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) 手册页。

### <a name="azcopy"></a>AzCopy
AzCopy 是一个命令行实用程序，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据。 AzCopy 操作简单：

1. 下载[最新版本的 AzCopy on Windows](http://aka.ms/downloadazcopy) 或 [AzCopy on Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy)。
2. 在命令行处使用 `azcopy` 将数据移动到 Azure 文件共享。 对于 Windows，其语法如下： 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    对于 Linux，命令语法稍有不同：

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy 提供丰富的选项，用户可根据需要修改复制行为。 有关详细信息，请参阅 [AzCopy on Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 和 [AzCopy on Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="automatically-mount-on-needed-pcsservers"></a>在所需电脑或服务器上自动装载
要替换本地文件共享，最好在将要使用的计算机上预先装载共享。 可在一系列计算机上自动完成此操作。

> [!Note]  
> 装载 Azure 文件共享需要使用存储帐户密钥作为密码，因此最好仅在受信任的环境中进行装载。 

### <a name="windows"></a>Windows
可使用 PowerShell 在多台电脑上运行装载命令。 在以下示例中，需要手动填充 `$computers`，但你可以生成要自动装载的计算机列表。 例如，可使用 Active Directory 中的结果填充此变量。

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
与 SSH 结合使用的简单 bash 脚本可以在以下示例中产生相同的结果。 同样，`$computer` 变量也需要用户手动填充：

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [排查 Windows 中的 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
- [排查 Linux 中的 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)