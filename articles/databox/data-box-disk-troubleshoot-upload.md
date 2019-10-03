---
title: Azure Data Box 磁盘故障排除数据上传使用日志的问题 |Microsoft Docs
description: 介绍如何使用日志和故障排除过程时将数据上载到 Azure Data Box 磁盘问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807492"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解日志排查在 Azure Data Box 磁盘中的数据上传问题

本文适用于 Microsoft Azure Data Box 磁盘，并介绍了将数据上传到 Azure 时，将显示的问题。

## <a name="about-upload-logs"></a>有关上传日志

在数据中心，将数据上载到 Azure 时`_error.xml`和`_verbose.xml`文件生成每个存储帐户。 这些日志上载到同一存储帐户用于将数据上传。 

这两种日志格式都相同，并且包含将数据从磁盘复制到 Azure 存储帐户时出现的事件的 XML 说明。

详细日志包含有关状态的每个 blob 或文件，复制操作的完整信息，而错误日志包含只为 blob 或文件在上传过程中遇到错误的信息。

错误日志包含详细日志，但筛选出成功的操作相同的结构。

## <a name="download-logs"></a>下载日志

执行以下步骤来找到上传日志。

1. 如果有任何错误时将数据上传到 Azure，门户会显示到诊断日志所在的文件夹路径。

    ![链接到在门户中的日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 转到**waies**。

    ![错误日志和详细日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每种情况下，可以看到的错误日志和详细日志。 选择每个日志和下载的本地副本。

## <a name="sample-upload-logs"></a>示例上传日志

一个示例`_verbose.xml`如下所示。 在这种情况下，顺序已成功完成，并且没有错误。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

为具有相同的顺序的一个示例`_error.xml`如下所示。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

一个示例`_error.xml`的位置如下所示完成但有错误的顺序。 

错误文件在这种情况下具有`Summary`部分和另一节，其中包含所有文件级别的错误。 

`Summary`包含`ValidationErrors`和`CopyErrors`。 在这种情况下，8 文件或文件夹上传到 Azure 并没有验证错误。 已将数据复制到 Azure 存储帐户，5 个文件或文件夹已成功上载。 剩余的 3 个文件或文件夹已根据 Azure 容器命名约定重命名，然后成功上载到 Azure。

文件级别状态位于`BlobStatus`描述将 blob 上传所采取的任何操作。 在这种情况下，因为数据复制到其中的文件夹不符合使用用于容器的 Azure 命名约定，将重命名三个容器。 有关这些容器中上传的 blob，将包含新的容器名称、 在 Azure 中的 blob 路径，原始文件路径无效和 blob 大小。
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>数据上传错误

下表总结了将数据上传到 Azure 时，生成的错误。

| 错误代码 | 描述                   |
|-------------|------------------------------|
|`None` |  已成功完成。           |
|`Renamed` | 已成功重命名 blob。   |
|`CompletedWithErrors` | 上传已完成但出现错误。 日志文件中包括错误中的文件的详细信息。  |
|`Corrupted`|在计算过程中的数据引入的 CRC 与在计算过程中上传的 CRC 不匹配。  |  
|`StorageRequestFailed` | Azure 存储请求失败。   |     
|`LeasePresent` | 此项租用，并且正由另一个用户。 |
|`StorageRequestForbidden` |无法上载由于身份验证问题。 |
|`ManagedDiskCreationTerminalFailure` | 无法上载为托管磁盘。 文件是可用的临时存储帐户中作为页 blob。 为托管磁盘，可以手动转换页 blob。  |
|`DiskConversionNotStartedTierInfoMissing` | 由于预创建的层文件夹外复制 VHD 文件，不被创建托管的磁盘。 将文件上载到指定的临时存储帐户的页 blob 作为创建订单过程。 为托管磁盘可以手动将其转换。|
|`InvalidWorkitem` | 无法上载数据，因为它不符合 Azure 命名，并限制约定。|
|`InvalidPageBlobUploadAsBlockBlob` | 作为具有前缀的容器的块 blob 上传`databoxdisk-invalid-pb-`。|
|`InvalidAzureFileUploadAsBlockBlob` | 作为具有前缀的容器的块 blob 上传`databoxdisk-invalid-af`-。|
|`InvalidManagedDiskUploadAsBlockBlob` | 作为具有前缀的容器的块 blob 上传`databoxdisk-invalid-md`-。|
|`InvalidManagedDiskUploadAsPageBlob` |具有前缀的容器中作为页 blob 上传`databoxdisk-invalid-md-`。 |
|`MovedToOverflowShare` |已上传的文件到新的共享作为原始的共享大小超出最大的 Azure 大小限制。 新的文件共享名称具有原始名称后面带有`-2`。   |
|`MovedToDefaultAzureShare` |没有任何默认共享文件夹的一部分的已上传的文件。 共享名开头`databox-`。 |
|`ContainerRenamed` |这些文件的容器不符合 Azure 命名约定，并且已重命名。 新名称开头`databox-`和 SHA1 哈希的原始名称作为后缀 |
|`ShareRenamed` |这些文件的共享不符合 Azure 命名约定，并已重命名。 新名称开头`databox-`和 SHA1 哈希的原始名称作为后缀。 |
|`BlobRenamed` |这些文件不符合 Azure 命名约定，并已重命名。 检查`BlobPath`字段中为新名称。 |
|`FileRenamed` |这些文件不符合 Azure 命名约定，并已重命名。 检查`FileStoragePath`字段中为新名称。 |
|`DiskRenamed` |这些文件不符合 Azure 命名约定，并已重命名。 检查`BlobPath`字段中为新名称。 |


## <a name="next-steps"></a>后续步骤

- [Data Box 磁盘问题开具支持票证](data-box-disk-contact-microsoft-support.md)。
