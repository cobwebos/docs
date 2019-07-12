---
title: Azure Data Box 磁盘故障排除 | Microsoft Docs
description: 介绍如何排查 Azure Data Box 磁盘中出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805704"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用日志来解决验证问题，Azure Data Box 磁盘

本文适用于 Microsoft Azure Data Box 磁盘。 本文介绍如何使用日志来解决部署此解决方案时，将无法显示的验证问题。

## <a name="validation-tool-log-files"></a>验证工具日志文件

验证使用的磁盘上的数据时[验证工具](data-box-disk-deploy-copy-data.md#validate-data)，则*error.xml*生成以记录任何错误。 日志文件位于`Drive:\DataBoxDiskImport\logs`驱动器的文件夹。 运行验证时提供错误日志的链接。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果你运行针对验证的多个会话，则一个错误日志生成每个会话。

- 以下是错误日志的示例时将数据加载到`PageBlob`文件夹不是 512 字节对齐。 所有递送到页 blob 上传数据必须都是 512 字节对齐，例如，VHD 或 VHDX。 此文件中的错误是在`<Errors>`中的和警告`<Warnings>`。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 容器名称无效时，下面是错误日志的一个示例。 下创建的文件夹`BlockBlob`， `PageBlob`，或`AzureFile`磁盘上的文件夹将成为你的 Azure 存储帐户中的容器。 容器名称必须遵循[Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>验证工具错误

中包含的错误*error.xml*与相应的建议的操作下表中汇总。

| 错误代码| 描述                       | 建议的操作               |
|------------|--------------------------|-----------------------------------|
| `None` | 已成功验证数据。 | 不需要执行任何操作。 |
| `InvalidXmlCharsInPath` |无法创建清单文件，如文件路径包含无效的字符。 | 删除这些字符以继续。  |
| `OpenFileForReadFailed`| 无法处理该文件。 这可能是由于访问问题或文件系统损坏。|无法读取的文件，因为出现错误。 错误详细信息是在异常中。 |
| `Not512Aligned` | 此文件不是有效的格式为 PageBlob 文件夹中。| 仅为 512 个字节的上传数据对齐到`PageBlob`文件夹。 从 PageBlob 文件夹中删除该文件或将其移动到 BlockBlob 文件夹。 重试验证。|
| `InvalidBlobPath` | 文件路径不会映射到有效的 blob 路径中根据 Azure Blob 命名约定云。|请遵循 Azure 命名的准则，若要重命名的文件路径。 |
| `EnumerationError` | 无法枚举进行验证的文件。 |可能有多个导致此错误的原因。 最可能的原因是对文件的访问。 |
| `ShareSizeExceeded` | 此文件导致 Azure 文件共享大小超过 5 TB 的 Azure 限制。|减小共享中的数据的大小，以便它符合[Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `AzureFileSizeExceeded` | 文件大小超出了 Azure 文件的大小限制。| 减小文件或数据的大小，以便它符合[Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。|
| `BlockBlobSizeExceeded` | 文件大小超出 Azure 块 Blob 大小限制。 | 减小文件或数据的大小，以便它符合[Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `ManagedDiskSizeExceeded` | 文件大小超出了 Azure 托管磁盘的大小限制。 | 减小文件或数据的大小，以便它符合[Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `PageBlobSizeExceeded` | 文件大小超出了 Azure 托管磁盘的大小限制。 | 减小文件或数据的大小，以便它符合[Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `InvalidShareContainerFormat`  |目录名称不符合的容器或共享的 Azure 命名约定。         |在磁盘上预先存在的文件夹下创建的第一个文件夹将成为你的存储帐户中的容器。 此共享或容器名称不符合 Azure 命名约定。 重命名该文件，以便它符合[Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。   |
| `InvalidBlobNameFormat` | 文件路径不会映射到有效的 blob 路径中根据 Azure Blob 命名约定云。|重命名该文件，以便它符合[Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。 |
| `InvalidFileNameFormat` | 文件路径不会映射到有效的文件路径中根据 Azure 文件命名约定云。 |重命名该文件，以便它符合[Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。 |
| `InvalidDiskNameFormat` | 文件路径不会映射到有效的磁盘名称中根据 Azure 托管磁盘命名约定云。 |重命名该文件，以便它符合[Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。       |
| `NotPartOfFileShare` | 文件上传路径不是有效的。 将文件上传到 Azure 文件中的文件夹。   | 在错误中删除的文件并将这些文件上传到预创建的文件夹。 重试验证。 |
| `NonVhdFileNotSupportedForManagedDisk` | 无法上载非 VHD 文件，作为托管磁盘。 |删除非 VHD 文件从`ManagedDisk`文件夹，因为这些不受支持或移动到这些文件`PageBlob`文件夹。 重试验证。 |


## <a name="next-steps"></a>后续步骤

- 进行故障排除[数据上传错误](data-box-disk-troubleshoot-upload.md)。
