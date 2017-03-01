---
title: "Azure 导入/导出清单文件格式 | Microsoft Docs"
description: "了解驱动器清单文件的格式。清单文件描述 Azure Blob 存储中的 Blob 与构成导入/导出服务中导入或导出作业的驱动器上的文件之间的映射。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: 2c76120a967aabf546fdb5246478f78e8cf47f94
ms.lasthandoff: 02/16/2017


---

# <a name="azure-importexport-service-manifest-file-format"></a>Azure 导入/导出服务清单文件格式
驱动器清单文件描述 Azure Blob 存储中的 Blob 与构成导入或导出作业的驱动器上的文件之间的映射。 对于某个导入操作而言，该清单文件作为驱动器准备过程的一部分创建，在将该驱动器送至 Azure 数据中心之前已存储在驱动器上。 在导出操作过程中，Azure 导入/导出服务将在驱动器上创建并存储该清单。  
  
对于导入和导出作业而言，驱动器清单文件存储在导入或导出驱动器上；该文件不通过任何 API 操作传输到该服务。  
  
下面介绍驱动器清单文件的一般格式：  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  
```

下表指定了驱动器清单 XML 格式的数据元素和属性。  
  
|XML 元素|类型|说明|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Root 元素|清单文件的根元素。 该文件中的其他所有元素均位于此元素下方。|  
|`Version`|属性，字符串|清单文件的版本。|  
|`Drive`|嵌套的 XML 元素|包含每个驱动器的清单。|  
|`DriveId`|String|驱动器的唯一驱动器标识符。 通过查询驱动器获取其序列号，即可找到该驱动器标识符。 该驱动器序列号通常还打印在该驱动器的外部。 `DriveID` 元素必须出现在清单元素中的任何 `BlobList` 元素之前。|  
|`StorageAccountKey`|String|当且仅当未指定 `ContainerSas` 时，导入作业才需要此元素。 与作业关联的 Azure 存储帐户的帐户密钥。<br /><br /> 导出操作的清单中会省略此元素。|  
|`ContainerSas`|String|当且仅当未指定 `StorageAccountKey` 时，导入作业才需要此元素。 用于访问与作业关联的 Blob 的容器 SAS。 有关其格式，请参阅[放置作业](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)。导出操作的清单中会省略此元素。|  
|`ClientCreator`|String|指定创建 XML 文件的客户端。 导入/导出服务不解释该值。|  
|`BlobList`|嵌套的 XML 元素|包含作为导入或导出作业一部分的 Blob 列表。 Blob 列表中的每个 Blob 共享相同的元数据和属性。|  
|`BlobList/MetadataPath`|String|可选。 指定磁盘上某个文件的相对路径，该文件包含针对导入操作的 Blob 列表中的 Blob 设置的默认元数据。 可以针对每个 Blob 有选择性地重写此元数据。<br /><br /> 导出操作的清单中会省略此元素。|  
|`BlobList/MetadataPath/@Hash`|属性，字符串|为元数据文件指定 Base16 编码 MD5 哈希值。|  
|`BlobList/PropertiesPath`|String|可选。 指定磁盘上某个文件的相对路径，该文件包含针对导入操作的 Blob 列表中的 Blob 设置的默认属性。 可以针对每个 Blob 有选择性地重写这些属性。<br /><br /> 导出操作的清单中会省略此元素。|  
|`BlobList/PropertiesPath/@Hash`|属性，字符串|为 properties 文件指定 Base16 编码 MD5 哈希值。|  
|`Blob`|嵌套的 XML 元素|包含每个 Blob 列表中每个 Blob 的相关信息。|  
|`Blob/BlobPath`|String|Blob 的相对 URI，以容器名称开头。 如果该 Blob 在根容器中，则必须以 `$root` 开头。|  
|`Blob/FilePath`|String|指定驱动器上文件的相对路径。 对于导出作业，如果可能，该 Blob 路径将用作文件路径；*例如*，`pictures/bob/wild/desert.jpg` 将导出到 `\pictures\bob\wild\desert.jpg`。 但是，由于 NTFS 名称的限制，Blob 可能会导出到路径并不像 Blob 路径的文件。|  
|`Blob/ClientData`|String|可选。 包含客户提供的注释。 导入/导出服务不解释该值。|  
|`Blob/Snapshot`|DateTime|对于导出作业而言是可选的。 指定某个已导出的 Blob 快照的快照标识符。|  
|`Blob/Length`|Integer|指定 Blob 的总长度（字节）。 对于块 Blob，该值最大可为 200 GB；对于页 Blob，该值最大可为 1 TB。 对于页 Blob，该值必须是 512 的倍数。|  
|`Blob/ImportDisposition`|String|对于导入作业而言是可选的，对于导出作业则可省略。 此元素指定当存在同名的 Blob 时，导入/导出作业应如何处理导入作业的大小写。 如果在导入清单中省略此值，将使用默认值 `rename`。<br /><br /> 此元素的值包括：<br /><br /> -   `no-overwrite`：如果已存在使用相同名称的目标 Blob，导入操作将跳过此文件的导入。<br />-   `overwrite`：现有目标 Blob 由新导入的文件完全覆盖。<br />-   `rename`：新 Blob 将使用修改的名称上传。<br /><br /> 重命名规则如下：<br /><br /> -   如果 Blob 名称中不包含句点，则通过将 `(2)` 附加到原始 Blob 名称中来生成新名称；如果这个新名称还与某个现有 Blob 名称冲突，则附加 `(3)` 来代替 `(2)`；依此类推。<br />-   如果 Blob 名称中包含句点，最后一个句点后面的部分则视为扩展名。 与上面的过程相似，`(2)` 将插入到最后一个句点前面来生成新名称；如果新名称仍与某个现有 Blob 名称冲突，服务将尝试附加 `(3)`、`(4)`，依此类推，直到创建一个不冲突的名称。<br /><br /> 下面是一些示例：<br /><br /> Blob `BlobNameWithoutDot` 将重命名为：<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Blob `Seattle.jpg` 将重命名为：<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|嵌套的 XML 元素|对于页 Blob 而言是必需的。<br /><br /> 对于导入操作，指定要导入的文件的字节范围列表。 每个页面范围由描述该页面范围的源文件中的偏移量和长度以及区域的 MD5 哈希描述。 页面范围的 `Hash` 属性是必需的。 服务将验证 Blob 中数据的哈希是否与根据页面范围计算出的 MD5 哈希匹配。 可以使用任意数目的页面范围来描述用于某个导入的文件，但总大小不能超过 1 TB。 所有页面范围都必须按偏移量排序，不允许重叠。<br /><br /> 对于导出操作，指定已导出到驱动器的 Blob 的一组字节范围。<br /><br /> 这些页面范围一起也可能仅涵盖某个 Blob 或文件的子范围。  该文件的剩余部分未由任何页面范围涵盖符合预期，其内容可能未定义。|  
|`PageRange`|XML 元素|表示页面范围。|  
|`PageRange/@Offset`|属性，整数|指定传输文件中的偏移量，以及指定的页面范围的起始 Blob。 此值必须是 512 的倍数。|  
|`PageRange/@Length`|属性，整数|指定页面范围的长度。 此值必须是 512 的倍数，并且不超过 4 MB。|  
|`PageRange/@Hash`|属性，字符串|为页面范围指定 Base16 编码 MD5 哈希值。|  
|`BlockList`|嵌套的 XML 元素|对于包含命名块的块 Blob 而言是必需的。<br /><br /> 对于导入操作而言，块列表指定要导入到 Azure 存储中的一组块。 对于导出操作而言，块列表指定每个块在导出磁盘上的文件中的存储位置。 每个块由该文件中的偏移量和块长度描述；每个块进一步由块 ID 属性命名，包含该块的 MD5 哈希。 最多可以使用 50,000 个块来描述一个 Blob。  所有块都必须按偏移量排序，它们应共同涵盖文件的完整范围，*也就是说*，块之间不能有间隙。 如果 Blob 未超过 64 MB，则每个块的块 ID 必须全有或全无。 块 ID 必须是 Base64 编码字符串。 有关块 ID 的其他要求，请参阅[放置块](/rest/api/storageservices/fileservices/put-block)。|  
|`Block`|XML 元素|表示块。|  
|`Block/@Offset`|属性，整数|指定特定块的起始偏移位置。|  
|`Block/@Length`|属性，整数|指定块中的字节数；此值不得超过 4MB。|  
|`Block/@Id`|属性，字符串|指定一个表示块 ID 的字符串。|  
|`Block/@Hash`|属性，字符串|指定块的 Base16 编码 MD5 哈希。|  
|`Blob/MetadataPath`|String|可选。 指定元数据文件的相对路径。 在导入过程中，将对目标 Blob 设置元数据。 在执行导出操作过程中，Blob 的元数据将存储在驱动器上的元数据文件中。|  
|`Blob/MetadataPath/@Hash`|属性，字符串|指定 Blob 元数据文件的 Base16 编码 MD5 哈希。|  
|`Blob/PropertiesPath`|String|可选。 指定 properties 文件的相对路径。 在导入过程中，将对目标 Blob 设置属性。 在执行导出操作过程中，Blob 属性将存储在驱动器上的 properties 文件中。|  
|`Blob/PropertiesPath/@Hash`|属性，字符串|指定 Blob properties 文件的 Base16 编码 MD5 哈希。|  
  
## <a name="see-also"></a>另请参阅  
[存储导入/导出 REST](/rest/api/storageimportexport/)

