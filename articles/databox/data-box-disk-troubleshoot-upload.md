---
title: Azure Data Box 磁盘故障排除数据上传使用日志的问题 |Microsoft Docs
description: 介绍如何使用日志和故障排除过程时将数据上载到 Azure Data Box 磁盘问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148304"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解日志排查在 Azure Data Box 磁盘中的数据上传问题

本文适用于 Microsoft Azure Data Box 磁盘，并介绍了将数据上传到 Azure 时，将显示的问题。

## <a name="data-upload-logs"></a>数据上传日志

在数据中心，将数据上载到 Azure 时`_error.xml`和`_verbose.xml`生成文件。 这些日志上载到同一存储帐户用于将数据上传。 一个示例`_error.xml`如下所示。
    
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

## <a name="download-logs"></a>下载日志

有两种方法来查找并下载诊断日志。

- 如果有任何错误时将数据上传到 Azure，门户会显示到诊断日志所在的文件夹路径。

    ![链接到在门户中的日志](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- 请转到与你的 Data Box 订单关联的存储帐户。 转到“Blob 服务”>“浏览 Blob”，查找对应于该存储帐户的 Blob。  转到**waies**。

    ![复制日志 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

在每种情况下，可以看到的错误日志和详细日志。 选择每个日志和下载的本地副本。


## <a name="data-upload-errors"></a>数据上传错误

下表总结了将数据上传到 Azure 时，生成的错误。

| 错误代码 | 描述                        |
|-------------|------------------------------|
|`None` |  已成功完成。           |
|`Renamed` | 已成功重命名 blob。  |                                                            |
|`CompletedWithErrors` | 上传已完成但出现错误。 日志文件中包括错误中的文件的详细信息。  |
|`Corrupted`|在计算过程中的数据引入的 CRC 与在计算过程中上传的 CRC 不匹配。  |  
|`StorageRequestFailed` | Azure 存储请求失败。   |     |
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
