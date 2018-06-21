---
title: 使用 Azure 导入/导出服务从 Azure Blob 导出数据 | Microsoft Docs
description: 了解如何在 Azure 门户中创建导出作业，以便从 Azure Blob 传输数据。
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: eb41708c7446b3139758678c9247ffbb11da8b40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659216"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>使用 Azure 导入/导出服务从 Azure Blob 存储导出数据
本文分步说明如何使用 Azure 导入/导出服务从 Azure Blob 存储安全地导出大量数据。 该服务要求你将空驱动器寄送到 Azure 数据中心。 该服务将数据从存储帐户导出到驱动器，然后将驱动器寄回。

## <a name="prerequisites"></a>先决条件

在创建导出作业以从 Azure Blob 存储传输数据之前，请仔细查看并完成以下此服务的先决条件列表。 必须：

- 拥有可用于导入/导出服务的有效 Azure 订阅。
- 拥有至少一个 Azure 存储帐户。 请参阅[导入/导出服务支持的存储帐户和存储类型](storage-import-export-requirements.md)的列表。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](storage-create-storage-account.md#create-a-storage-account)。
- 拥有足够数量的[受支持类型](storage-import-export-requirements.md#supported-disks)的磁盘。

## <a name="step-1-create-an-export-job"></a>步骤 1：创建导出作业

在 Azure 门户中执行以下步骤来创建导出作业。

1. 登录到 https://portal.azure.com/。
2. 转到“所有服务”>“存储”>“导入/导出作业”。 

    ![转到导入/导出作业](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. 单击“创建导入/导出作业”。

    ![单击导入/导出作业](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. 在“基本信息”中：
    
    - 选择“从 Azure 导出”。 
    - 为导出作业输入一个描述性名称。 使用所选名称来跟踪作业进度。 
        - 此名称只能包含小写字母、数字、连字符和下划线。
        - 此名称必须以字母开头，并且不得包含空格。 
    - 选择一个订阅。
    - 输入或选择一个资源组。

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. 在“作业详细信息”中：

    - 选择要导出的数据所在的存储帐户。 
    - 放置位置根据选定存储帐户所属的区域自动进行填充。 
    - 指定要从存储帐户导出到空驱动器的 blob 数据。 
    - 选择“全部导出”以导出存储帐户中的所有 blob 数据。
    
         ![全部导出](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - 可以指定要导出的容器和 blob。
        - **指定要导出的 blob**：使用“等于”选择器。 指定 blob 的相对路径，以容器名称开头。 使用 *$root* 指定根容器。
        - **指定以前缀开头的所有 blob**：使用“开头为”选择器。 指定以正斜杠“/”开头的前缀。 该前缀可以是容器名称的前缀、完整容器名称或者后跟 Blob 名称前缀的完整容器名称。 必须以有效格式提供 blob 路径，以免在处理过程中出现错误，如以下屏幕截图所示。 有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。 
   
           ![导出所选容器和 blob](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - 可以从 blob 列表文件进行导出。

        ![从 blob 列表文件导出](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。
 

4. 在“回寄信息”中：

    - 从下拉列表中选择承运人。
    - 输入为该承运人创建的有效承运人帐号。 导入作业完成后，Microsoft 使用此帐户寄回驱动器。 
    - 提供完整、有效的联系人姓名、电话号码、电子邮件地址、街道地址、城市、邮政编码、省/自治区/直辖市和国家/地区。
   
5. 在“摘要”中：

    - 查看作业详细信息。
    - 记下作业名称以及为将磁盘寄送到 Azure 而提供的 Azure 数据中心寄送地址。 
    - 单击“确定”以完成导出作业的创建。

## <a name="step-2-ship-the-drives"></a>步骤 2：寄送驱动器

如果不知道所需的驱动器数量，请转到[检查驱动器数量](#check-the-number-of-drives)。 如果知道驱动器数量，则直接寄送驱动器。

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>步骤 3：使用跟踪信息更新作业

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>步骤 4：接收磁盘
当仪表板报告作业已完成时，会将磁盘寄送给你，并且门户上会提供货件的跟踪号。

1. 收到包含导出数据的驱动器后，你需要获取 BitLocker 密钥才能解锁驱动器。 转到 Azure 门户中的导出作业。 单击“导入/导出”选项卡。 
2. 从列表中选择并单击导出作业。 转到“BitLocker 密钥”并复制密钥。
   
   ![查看导出作业的 BitLocker 密钥](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. 使用 BitLocker 密钥解锁磁盘。

导出已完成。 此时可以手动删除作业，或在 90 天后自动删除作业。


## <a name="check-the-number-of-drives"></a>检查驱动器数量

此*可选*步骤有助于确定导出作业所需的驱动器数量。 在运行[受支持 OS 版本](storage-import-export-requirements.md#supported-operating-systems)的 Windows 系统上执行此步骤。

1. 在 Windows 系统上[下载 WAImportExport 版本 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659)。 
2. 解压缩到默认文件夹 `waimportexportv1`。 例如，`C:\WaImportExportV1`。
3. 使用管理权限打开 PowerShell 或命令行窗口。 若要将目录切换到解压缩的文件夹，请运行以下命令：
    
    `cd C:\WaImportExportV1`

4. 若要检查选定 blob 所需的磁盘数量，请运行以下命令：

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    下表介绍了这些参数：
    
    |命令行参数|说明|  
    |--------------------------|-----------------|  
    |**/logdir:**|可选。 日志目录。 详细日志文件将写入此目录。 如果未指定，则使用当前目录作为日志目录。|  
    |**/sn:**|必需。 导出作业的存储帐户的名称。|  
    |**/sk:**|仅当未指定容器 SAS 时才是必需的。 导出作业的存储帐户的帐户密钥。|  
    |**/csas:**|仅当未指定存储帐户密钥时才是必需的。 用于列出要在导出作业中导出的 Blob 的容器 SAS。|  
    |**/ExportBlobListFile:**|必需。 包含要导出的 Blob 的 Blob 路径列表或 Blob 路径前缀的 XML 文件的路径。 导入/导出服务 REST API 的[放置作业](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)操作的 `BlobListBlobPath` 元素中使用的文件格式。|  
    |**/DriveSize:**|必需。 用于导出作业的驱动器大小，*例如* 500 GB、1.5 TB。|  

    请参阅 [PreviewExport 命令示例](#example-of-previewexport-command)。
 
5. 检查能否读取/写入要寄送的用于导出作业的驱动器。

### <a name="example-of-previewexport-command"></a>PreviewExport 命令示例

以下示例演示了 `PreviewExport` 命令：  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
导出 Blob 列表文件可能包含 Blob 名称和 Blob 前缀，如下所示：  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure 导入/导出工具可列出要导出的所有 blob，在考虑所有必要开销的情况下计算如何将其打包到指定大小的驱动器，然后估算保存 blob 和驱动器使用情况信息所需的驱动器数量。  
  
下面是一个省略了信息性日志的输出示例：  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>有效 blob 路径示例

下表显示有效 Blob 路径的示例：
   
   | 选择器 | Blob 路径 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/book |导出任何容器中以前缀 **book** 开头的所有 Blob |
   | 开头为 |/music/ |导出容器 **music** 中的所有 Blob |
   | 开头为 |/music/love |导出容器 **music** 中以前缀 **love** 开头的所有 Blob |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob **logo.bmp** |
   | 等于 |videos/story.mp4 |导出容器 **videos** 中的 Blob **story.mp4** |

## <a name="next-steps"></a>后续步骤

* [查看作业和驱动器状态](storage-import-export-view-drive-status.md)
* [查看导入/导出要求](storage-import-export-requirements.md)


