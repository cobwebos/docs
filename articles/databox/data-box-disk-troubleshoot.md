---
title: Azure Data Box 磁盘故障排除 | Microsoft Docs
description: 介绍如何排查 Azure Data Box 磁盘中出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 35b4b206de9c16d66387135b0ca75b6aaeb7c744
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564999"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用日志排查 Azure Data Box Disk 中的验证问题

本文适用于 Microsoft Azure Data Box Disk。 本文介绍如何使用日志来排查部署此解决方案时可能会出现的验证问题。

## <a name="validation-tool-log-files"></a>验证工具日志文件

使用[验证工具](data-box-disk-deploy-copy-data.md#validate-data)验证磁盘上的数据时，会生成一个 *error.xml* 文件来记录任何错误。 该日志文件位于驱动器的 `Drive:\DataBoxDiskImport\logs` 文件夹中。 当你运行验证时，系统会提供错误日志的链接。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果运行多个会话进行验证，将为每个会话生成一个错误日志。

- 下面是当载入 `PageBlob` 文件夹的数据未经 512 字节对齐时生成的错误日志示例。 上传到 PageBlob 的任何数据（例如 VHD 或 VHDX）必须经过 512 字节对齐。 此文件中的错误位于 `<Errors>` 中，警告位于 `<Warnings>` 中。

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

- 下面是当容器名称无效时生成的错误日志示例。 在磁盘上的 `BlockBlob`、`PageBlob` 或 `AzureFile` 文件夹下创建的文件夹将成为 Azure 存储帐户中的容器。 容器的名称必须遵守 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

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

下表汇总了 *error.xml* 中包含的错误以及相应的建议操作。

| 错误代码| 说明                       | 建议的操作               |
|------------|--------------------------|-----------------------------------|
| `None` | 已成功验证数据。 | 不需要执行任何操作。 |
| `InvalidXmlCharsInPath` |无法创建清单文件，因为文件路径包含无效字符。 | 请删除这些字符以继续。  |
| `OpenFileForReadFailed`| 无法处理文件。 原因可能是出现访问问题或文件系统损坏。|由于出错，无法读取文件。 错误详细信息已显示在异常中。 |
| `Not512Aligned` | 此文件的格式无效，无法上传到 PageBlob 文件夹。| 请仅将 512 字节对齐的数据上传到 `PageBlob` 文件夹。 从 PageBlob 文件夹中删除该文件，或将其移到 BlockBlob 文件夹。 重试验证。|
| `InvalidBlobPath` | 文件路径不会按照 Azure Blob 命名约定映射到云中的有效 blob 路径。|请遵照 Azure 命名准则重命名文件路径。 |
| `EnumerationError` | 无法枚举要验证的文件。 |有多种可能的原因会导致此错误。 最可能的原因是对文件的访问权限有问题。 |
| `ShareSizeExceeded` | 此文件导致 Azure 文件共享大小超过 Azure 限制 (5 TB)。|请减小共享中的数据大小，使其符合 [Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `AzureFileSizeExceeded` | 文件大小超过了 Azure 文件大小限制。| 请减小文件或数据的大小，使其符合 [Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。|
| `BlockBlobSizeExceeded` | 文件大小超过了 Azure 块 Blob 大小限制。 | 请减小文件或数据的大小，使其符合 [Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `ManagedDiskSizeExceeded` | 文件大小超过了 Azure 托管磁盘大小限制。 | 请减小文件或数据的大小，使其符合 [Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `PageBlobSizeExceeded` | 文件大小超过了 Azure 托管磁盘大小限制。 | 请减小文件或数据的大小，使其符合 [Azure 对象大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重试验证。 |
| `InvalidShareContainerFormat`  |目录名称不符合容器或共享的 Azure 命名约定。         |在磁盘上现有文件夹下创建的第一个文件夹将成为存储帐户中的容器。 此共享或容器名称不符合 Azure 命名约定。 请重命名该文件，使其符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。   |
| `InvalidBlobNameFormat` | 文件路径不会按照 Azure Blob 命名约定映射到云中的有效 blob 路径。|请重命名该文件，使其符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。 |
| `InvalidFileNameFormat` | 文件路径未按 Azure 文件命名约定映射到云中的有效文件路径。 |请重命名该文件，使其符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。 |
| `InvalidDiskNameFormat` | 文件路径并未按照 Azure 托管磁盘命名约定映射到云中的有效磁盘名称。 |请重命名该文件，使其符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重试验证。       |
| `NotPartOfFileShare` | 文件的上传路径无效。 请将文件上传到 Azure 文件中的文件夹。   | 请删除出错文件，并将其上传到预先创建的文件夹。 重试验证。 |
| `NonVhdFileNotSupportedForManagedDisk` | 不能将非 VHD 文件作为托管磁盘上传。 |请从 `ManagedDisk` 文件夹中删除非 VHD 文件（因为不支持这些文件），或将其移到 `PageBlob` 文件夹。 重试验证。 |


## <a name="next-steps"></a>后续步骤

- 排查[数据上传错误](data-box-disk-troubleshoot-upload.md)。
