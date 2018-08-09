---
title: 使用 Azure 导入/导出将数据传输到 Azure 文件 | Microsoft Docs
description: 了解如何在 Azure 门户中创建导入作业，以便将数据传输到 Azure 文件。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: d6626b0c27f28f382d0189251fe90879020a69bf
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523625"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>使用 Azure 导入/导出服务将数据导入到 Azure 文件

本文提供了有关如何使用 Azure 导入/导出服务安全地将大量数据导入到 Azure 文件的分步说明。 若要导入数据，此服务要求你将包含数据的受支持磁盘驱动器寄送到某个 Azure 数据中心。  

导入/导出服务仅支持将 Azure 文件导入到 Azure 存储。 不支持导出 Azure 文件。

## <a name="prerequisites"></a>先决条件

在创建导入作业来将数据传输到 Azure 文件之前，请仔细查看并完成以下先决条件列表。 必须：

- 拥有可以用于导入/导出服务的活动 Azure 订阅。
- 拥有至少一个 Azure 存储帐户。 请参阅[导入/导出服务支持的存储帐户和存储类型](storage-import-export-requirements.md)的列表。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](storage-create-storage-account.md#create-a-storage-account)。
- 拥有足够数量的[受支持类型](storage-import-export-requirements.md#supported-disks)的磁盘。 
- 拥有运行[受支持 OS 版本](storage-import-export-requirements.md#supported-operating-systems)的 Windows 系统。
- 在 Windows 系统上[下载 WAImportExport 版本 2](https://www.microsoft.com/download/details.aspx?id=55280)。 解压缩到默认文件夹 `waimportexport`。 例如，`C:\WaImportExport`。
- 具有 FedEx/DHL 帐户。 
    - 该帐户必须是有余额的有效帐户，且有退货功能。
    - 生成导出作业的跟踪号。
    - 每个作业都应有一个单独的跟踪号。 不支持多个作业共享相同跟踪号。
    - 如果没有承运商帐户，请转到：
        - [创建 FedEX 帐户](https://www.fedex.com/en-us/create-account.html)，或 
        - [创建 DHL 帐户](http://www.dhl-usa.com/en/express/shipping/open_account.html)。
 


## <a name="step-1-prepare-the-drives"></a>步骤 1：准备驱动器

此步骤生成一个日志文件。 日志文件存储着驱动器序列号、加密密钥和存储帐户详细信息等基本信息。

请执行以下步骤来准备驱动器。

1. 通过 SATA 连接器将磁盘驱动器连接到 Windows 系统。
2. 在每个驱动器上创建一个 NTFS 卷。 为卷分配驱动器号。 不要使用装入点。
3. 修改工具所在的根文件夹中的 *dataset.csv* 文件。 根据是要导入文件还是文件夹还是同时导入两者，在 *dataset.csv* 文件中添加类似于以下示例的条目。  

    - **导入文件**：在以下示例中，要复制的数据位于 C: 驱动器中。 文件 *MyFile1.txt* 将被复制到根目录 *MyAzureFileshare1* 中。 如果 *MyAzureFileshare1* 不存在，则会在 Azure 存储帐户中创建该目录。 文件夹结构保持不变。

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **导入文件夹**：*MyFolder2* 下的所有文件和文件夹将以递归方式复制到该文件共享。 文件夹结构保持不变。

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    可以在同一文件中创建与导入的文件夹或文件对应的多个条目。 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    详细了解如何[准备数据集 CSV 文件](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)。
    

4. 修改工具所在的根文件夹中的 *driveset.csv* 文件。 在 *driveset.csv* 文件中添加类似于以下示例的条目。 此驱动器集文件包含磁盘列表和对应的驱动器号，因此，工具可以正确地选取要准备的磁盘列表。

    此示例假定将附加两个磁盘并创建基本 NTFS 卷 G:\ 和 H:\。 H:\ 未加密，而 G: 已加密。 该工具仅会对承载着 H:\（不会对承载着 G:\)）的磁盘进行格式化和加密。

    - **对于未加密的磁盘**：请指定 *Encrypt* 以在磁盘上启用 BitLocker 加密。

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **对于已加密的磁盘**：请指定 *AlreadyEncrypted* 并提供 BitLocker 密钥。

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    可以在同一文件中创建与多个驱动器对应的多个条目。 详细了解如何[准备驱动器集 CSV 文件](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)。 

5.  使用 `PrepImport` 选项将数据复制到磁盘驱动器并做好准备。 为了使第一个复制会话通过新的复制会话复制目录和/或文件，请运行以下命令：

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    下面显示了一个导入示例。
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. 每次运行该命令行时，都会使用通过 `/j:` 参数提供名称创建一个日志文件。 你准备的每个驱动器都有一个在创建导入作业时必须上传的日志文件。 不会处理没有日志文件的驱动器。

    > [!IMPORTANT]
    > - 完成磁盘准备工作后，请不要改磁盘驱动器上的数据，也不要修改日志文件。

有关更多示例，请转到[日志文件示例](#samples-for-journal-files)。

## <a name="step-2-create-an-import-job"></a>步骤 2：创建导入作业 

在 Azure 门户中执行以下步骤来创建导入作业。
1. 登录到 https://portal.azure.com/。
2. 转到“所有服务”>“存储”>“导入/导出作业”。 

    ![转到“导入/导出”](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. 单击“创建导入/导出作业”。

    ![单击导入/导出作业](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. 在“基本信息”中：

    - 选择“导入到 Azure”。
    - 输入导入作业的描述性名称。 在作业进行过程中以及作业完成后，可以使用此名称来跟踪作业。
        -  此名称只能包含小写字母、数字、连字符和下划线。
        -  此名称必须以字母开头，并且不得包含空格。 
    - 选择一个订阅。
    - 选一个择资源组。 

        ![创建导入作业 - 步骤 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. 在“作业详细信息”中：
    
    - 上传你在前面的[步骤 1：准备驱动器](#step-1-prepare-the-drives)中创建的日志文件。 
    - 选择要将数据导入到的存储帐户。 
    - 放置位置根据选定存储帐户所属的区域自动进行填充。
   
       ![创建导入作业 - 步骤 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. 在“回寄信息”中：

    - 从下拉列表中选择承运商。
    - 输入你已在该承运商那里创建的有效承运商帐户编号。 导入作业完成后，Microsoft 使用此帐户寄回驱动器。 
    - 提供完整、有效的联系人姓名、电话号码、电子邮件地址、街道地址、城市、邮政编码、省/自治区/直辖市和国家/地区。

        > [!TIP] 
        > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

       ![创建导入作业 - 步骤 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. 在“摘要”中：

    - 提供用来将磁盘寄回 Azure 的 Azure 数据中心送货地址。 请确保寄送标签上标明了作业名称和完整地址。
    - 单击“确定”以完成导入作业创建。

        ![创建导入作业 - 步骤 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>步骤 3：将驱动器寄送到 Azure 数据中心 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>步骤 4：使用跟踪信息更新作业

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>步骤 5：验证数据上传到 Azure

跟踪作业直至完成。 作业完成后，验证数据已上传到 Azure。 仅在已确认上传成功后才删除本地数据。

## <a name="samples-for-journal-files"></a>日志文件示例

若要**添加更多驱动器**，请创建一个新的驱动器集文件并运行以下命令。 

如果后续复制会话中的磁盘驱动器与 *InitialDriveset .csv* 文件中指定的不同，请指定一个新的驱动器集 *.csv* 文件并将其提供为参数 `AdditionalDriveSet` 的值。 使用**同一日记文件**的名称并提供**新的会话 ID**。 AdditionalDriveset CSV 文件的格式与 InitialDriveSet 的格式相同。

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

下面显示了一个导入示例。

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


若要向同一驱动器集添加更多数据，请为后续复制会话使用 PrepImport 命令来复制更多文件/目录。

在后续复制会话中将数据复制到 *InitialDriveset.csv* 文件中指定的同一组硬盘驱动器时，请指定**同一日志文件**名称并提供**新的会话 ID**；不需要提供存储帐户密钥。

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

下面显示了一个导入示例。

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>后续步骤

* [查看作业和驱动器状态](storage-import-export-view-drive-status.md)
* [查看导入/导出要求](storage-import-export-requirements.md)


