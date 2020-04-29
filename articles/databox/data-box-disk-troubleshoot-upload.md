---
title: 使用日志排查数据上传问题
titleSuffix: Azure Data Box Disk
description: 介绍如何使用日志以及排查将数据上传到 Azure Data Box Disk 时出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260132"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解如何使用日志排查将数据上传到 Azure Data Box Disk 时出现的问题

本文适用于 Microsoft Azure Data Box Disk 并介绍将数据上传到 Azure 时看到的问题。

## <a name="about-upload-logs"></a>关于上传日志

将数据上传到数据中心内的 Azure 后，系统将为每个存储帐户生成 `_error.xml` 和 `_verbose.xml` 文件。 这些日志将上传到用于上传数据的同一个存储帐户。 

这两份日志采用相同的格式，包含将数据从磁盘复制到 Azure 存储帐户时所发生的事件的 XML 描述。

详细日志包含有关每个 Blob 或文件的复制操作状态的完整信息，而错误日志仅包含上传期间遇到错误的 Blob 或文件的信息。

错误日志的结构与详细日志相同，但可在其中筛选出成功的操作。

## <a name="download-logs"></a>下载日志

执行以下步骤找到上传日志。

1. 如果在将数据上传到 Azure 时出现任何错误，门户会显示诊断日志所在的文件夹的路径。

    ![门户中日志的链接](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 转到“waies”。 

    ![错误和详细日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每种情况下，你都会看到错误日志和详细日志。 选择每个日志并下载本地副本。

## <a name="sample-upload-logs"></a>示例上传日志

下面显示了 `_verbose.xml` 的示例。 在本例中，订单已成功完成而未出错。

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

下面显示了同一订单的 `_error.xml` 示例。

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

下面显示了 `_error.xml` 的示例，其中的订单已完成但出错。 

在本例中，错误文件提供了一个 `Summary` 节，以及包含所有文件级错误的另一个节。 

`Summary` 包含 `ValidationErrors` 和 `CopyErrors`。 在本例中，已将 8 个文件或文件夹上传到 Azure，且未出现验证错误。 将数据复制到 Azure 存储帐户时，已成功上传 5 个文件或文件夹。 剩余的 3 个文件或文件夹已根据 Azure 容器命名约定重命名，然后成功上传到 Azure。

文件级状态位于 `BlobStatus` 中，描述了上传 Blob 所执行的任何操作。 在本例中，已重命名 3 个容器，因为数据复制到的文件夹不符合容器的 Azure 命名约定。 对于在这些容器中上传的 Blob，已包含新的容器名称、Azure 中 Blob 的路径、原始的无效文件路径和 Blob 大小。
    
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

下表汇总了将数据上传到 Azure 时生成的错误。

| 错误代码 | 说明                   |
|-------------|------------------------------|
|`None` |  已成功完成。           |
|`Renamed` | 已成功重命名 Blob。   |
|`CompletedWithErrors` | 上传已完成但出错。 错误中的文件的详细信息已包含在日志文件中。  |
|`Corrupted`|数据引入期间计算出的 CRC 与上传期间计算出的 CRC 不匹配。  |  
|`StorageRequestFailed` | Azure 存储请求失败。   |     
|`LeasePresent` | 此项是租用的，已被其他用户使用。 |
|`StorageRequestForbidden` |身份验证问题导致无法上传。 |
|`ManagedDiskCreationTerminalFailure` | 无法作为托管磁盘上传。 文件已在临时存储帐户中作为页 Blob 提供。 可将页 Blob 手动转换为托管磁盘。  |
|`DiskConversionNotStartedTierInfoMissing` | 由于 VHD 文件是在预创建的层文件夹外部复制的，因此未创建托管磁盘。 文件已作为页 Blob 上传到创建订单期间指定的临时存储帐户。 可手动将其转换为托管磁盘。|
|`InvalidWorkitem` | 无法上传数据，因为它不符合 Azure 命名约定和限制。|
|`InvalidPageBlobUploadAsBlockBlob` | 已作为块 Blob 上传到带有前缀 `databoxdisk-invalid-pb-` 的容器中。|
|`InvalidAzureFileUploadAsBlockBlob` | 已作为块 Blob 上传到带有前缀 `databoxdisk-invalid-af`- 的容器中。|
|`InvalidManagedDiskUploadAsBlockBlob` | 已作为块 Blob 上传到带有前缀 `databoxdisk-invalid-md`- 的容器中。|
|`InvalidManagedDiskUploadAsPageBlob` |已作为页 Blob 上传到带有前缀 `databoxdisk-invalid-md-` 的容器中。 |
|`MovedToOverflowShare` |由于原始共享大小超过了最大 Azure 大小限制，已将文件上传到新共享。 新文件共享名称包含带有 `-2` 后缀的原始名称。   |
|`MovedToDefaultAzureShare` |已将不包含在任何文件夹中的文件上传到默认共享。 共享名称以 `databox-` 开头。 |
|`ContainerRenamed` |这些文件的容器不符合 Azure 命名约定，现已重命名。 新名称以 `databox-` 开头，并以原始名称的 SHA1 哈希为后缀 |
|`ShareRenamed` |这些文件的共享不符合 Azure 命名约定，现已重命名。 新名称以 `databox-` 开头，并以原始名称的 SHA1 哈希为后缀。 |
|`BlobRenamed` |这些文件不符合 Azure 命名约定，已重命名。 请检查新名称的 `BlobPath` 字段。 |
|`FileRenamed` |这些文件不符合 Azure 命名约定，已重命名。 请检查新名称的 `FileStoragePath` 字段。 |
|`DiskRenamed` |这些文件不符合 Azure 命名约定，已重命名。 请检查新名称的 `BlobPath` 字段。 |


## <a name="next-steps"></a>后续步骤

- [为 Data Box Disk 问题打开支持票证](data-box-disk-contact-microsoft-support.md)。
