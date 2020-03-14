---
title: 使用日志排查数据上传问题
titleSuffix: Azure Data Box Disk
description: 介绍如何使用日志和解决将数据上载到 Azure Data Box Disk 时所见到的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260132"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解用于排查 Azure Data Box Disk 中的数据上传问题的日志

本文适用于 Microsoft Azure Data Box Disk 并介绍将数据上传到 Azure 时看到的问题。

## <a name="about-upload-logs"></a>关于上传日志

数据在数据中心上传到 Azure 时，会为每个存储帐户生成 `_error.xml` 和 `_verbose.xml` 文件。 这些日志将上载到用于上传数据的同一存储帐户。 

这两个日志的格式相同，并包含将数据从磁盘复制到 Azure 存储帐户时所发生事件的 XML 描述。

详细日志包含有关每个 blob 或文件的复制操作状态的完整信息，而错误日志只包含上传过程中遇到错误的 blob 或文件的信息。

错误日志与详细日志具有相同的结构，但会筛选出成功的操作。

## <a name="download-logs"></a>下载日志

执行以下步骤以查找上载日志。

1. 如果在将数据上传到 Azure 时出现任何错误，门户将显示诊断日志所在的文件夹的路径。

    ![链接到门户中的日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 请参阅**waies**。

    ![错误和详细日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每种情况下，都会看到错误日志和详细日志。 选择每个日志并下载本地副本。

## <a name="sample-upload-logs"></a>示例上传日志

下面显示了 `_verbose.xml` 的示例。 在这种情况下，订单已成功完成，无任何错误。

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

对于同一顺序，`_error.xml` 的示例如下所示。

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

下面显示了 `_error.xml` 的示例，其中订单已完成，但出现错误。 

在此示例中，错误文件有一个 `Summary` 部分以及另一个包含所有文件级别错误的部分。 

`Summary` 包含 `ValidationErrors` 和 `CopyErrors`。 在这种情况下，8个文件或文件夹已上传到 Azure，但没有任何验证错误。 将数据复制到 Azure 存储帐户时，5个文件或文件夹已成功上传。 其余3个文件或文件夹已根据 Azure 容器命名约定重命名，然后成功上传到 Azure。

文件级别状态位于 `BlobStatus` 中，描述了上载 blob 时执行的任何操作。 在这种情况下，将重命名三个容器，因为数据复制到的文件夹不符合容器的 Azure 命名约定。 对于在这些容器中上传的 blob，将包含新的容器名称、Azure 中 blob 的路径、原始无效文件路径和 blob 大小。
    
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

下表总结了将数据上传到 Azure 时生成的错误。

| 错误代码 | 说明                   |
|-------------|------------------------------|
|`None` |  已成功完成。           |
|`Renamed` | 已成功重命名 blob。   |
|`CompletedWithErrors` | 上传已完成，但出现错误。 日志文件中包含错误文件的详细信息。  |
|`Corrupted`|数据引入期间计算得出的 CRC 与上传过程中计算得出的 CRC 不匹配。  |  
|`StorageRequestFailed` | Azure 存储请求失败。   |     
|`LeasePresent` | 此项目已租给其他用户使用。 |
|`StorageRequestForbidden` |由于身份验证问题，无法上传。 |
|`ManagedDiskCreationTerminalFailure` | 无法作为托管磁盘上传。 文件在临时存储帐户中作为页 blob 提供。 可以将页 blob 手动转换为托管磁盘。  |
|`DiskConversionNotStartedTierInfoMissing` | 由于 VHD 文件是在预创建层文件夹之外复制的，因此不会创建托管磁盘。 文件作为页 blob 上传到创建订单期间指定的临时存储帐户。 可以手动将其转换为托管磁盘。|
|`InvalidWorkitem` | 无法上载数据，因为它不符合 Azure 命名和限制约定。|
|`InvalidPageBlobUploadAsBlockBlob` | 作为块 blob 上传到带有前缀 `databoxdisk-invalid-pb-`的容器中。|
|`InvalidAzureFileUploadAsBlockBlob` | 作为块 blob 上传，前缀为 `databoxdisk-invalid-af`-。|
|`InvalidManagedDiskUploadAsBlockBlob` | 作为块 blob 上传，前缀为 `databoxdisk-invalid-md`-。|
|`InvalidManagedDiskUploadAsPageBlob` |已上载为包含前缀 `databoxdisk-invalid-md-`的容器中的页 blob。 |
|`MovedToOverflowShare` |已将文件上载到新共享，因为原始共享大小超出了最大 Azure 大小限制。 新的文件共享名的原始名称带有后缀 `-2`。   |
|`MovedToDefaultAzureShare` |将不属于任何文件夹的文件上传到默认共享。 共享名称以 `databox-`开头。 |
|`ContainerRenamed` |这些文件的容器不符合 Azure 命名约定，已重命名。 新名称以 `databox-` 开头，并以原始名称的 SHA1 哈希为后缀 |
|`ShareRenamed` |这些文件的共享不符合 Azure 命名约定，已重命名。 新名称以 `databox-` 开头，并以原始名称的 SHA1 哈希为后缀。 |
|`BlobRenamed` |这些文件不符合 Azure 命名约定，已重命名。 检查 "`BlobPath`" 字段中是否有新的名称。 |
|`FileRenamed` |这些文件不符合 Azure 命名约定，已重命名。 检查 "`FileStoragePath`" 字段中是否有新的名称。 |
|`DiskRenamed` |这些文件不符合 Azure 命名约定，已重命名。 检查 "`BlobPath`" 字段中是否有新的名称。 |


## <a name="next-steps"></a>后续步骤

- [为 Data Box Disk 问题打开支持票证](data-box-disk-contact-microsoft-support.md)。
