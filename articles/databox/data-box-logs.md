---
title: 跟踪和记录 Azure Data Box 与 Azure Data Box Heavy 事件 | Microsoft Docs
description: 介绍如何在 Azure Data Box 与 Azure Data Box Heavy 订单的各个处理阶段跟踪和记录事件。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 8fecc00a970f0e706dc6240eaec593fd54968ff8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934201"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 和 Azure Data Box Heavy 的跟踪与事件日志记录

Data Box 或 Data Box Heavy 订单会经历以下步骤：订购、设置、数据复制、寄回、上传到 Azure、验证和数据擦除。 对于每个订单步骤，可以采取多种措施来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

下表汇总了 Data Box 或 Data Box Heavy 订单步骤，以及在每个步骤中可用于跟踪和审核订单的工具。

| Data Box 订单阶段       | 用于跟踪和审核的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 创建订单               | [通过 RBAC 对订单设置访问控制](#set-up-access-control-on-the-order)                                                    |
| 订单已处理            | 通过以下方式[跟踪订单](#track-the-order) <ul><li> Azure 门户 </li><li> 承运商网站 </li><li>电子邮件通知</ul> |
| 设置设备              | 在[活动日志](#query-activity-logs-during-setup)中记录的设备访问凭据                                              |
| 将数据复制到设备        | [查看 *error.xml* 文件](#view-error-log-during-data-copy)了解数据复制状态                                                             |
| 准备交付            | [检查 BOM 文件](#inspect-bom-during-prepare-to-ship)或设备上的清单文件                                      |
| 将数据上传到 Azure       | 查看在 Azure 数据中心内数据上传过程中[复制日志](#review-copy-log-during-upload-to-azure)是否出错                         |
| 从设备中擦除数据   | [查看监护日志链](#get-chain-of-custody-logs-after-data-erasure)，包括审核日志和订单历史记录                |

本文将详细介绍用于跟踪和审核 Data Box 或 Data Box Heavy 订单的各种机制或工具。 本文中的信息同时适用于 Data Box 和 Data Box Heavy。 在后续部分，有关 Data Box 的任何参考信息也适用于 Data Box Heavy。

## <a name="set-up-access-control-on-the-order"></a>针对订单设置访问控制

首次创建订单时，可以控制谁能够访问你的订单。 在不同的范围设置基于角色的访问控制 (RBAC)，以控制对 Data Box 订单的访问。 RBAC 角色确定了对一部分操作的访问类型 – 读写、只读、只写。

可为 Azure Data Box 服务定义的两个角色：

- **Data Box 读取者** - 对订单拥有按范围定义的只读访问权限。 他们只能查看订单详细信息。 他们无法访问与存储帐户相关的任何其他详细信息，也无法编辑订单详细信息，例如地址等。
- **Data Box 参与者** - 仅当已对某个存储帐户拥有写入访问权限时，才能创建一个订单以将数据传输到给定的存储帐户。 如果他们没有某个存储帐户的访问权限，则无法 Data Box 订单以将数据复制到该帐户。 此角色不会定义任何存储帐户相关的权限，也不授予对存储帐户的访问权限。  

若要限制对订单的访问，可以：

- 在订单级别分配一个角色。 用户只拥有角色定义的权限，只能与该特定的 Data Box 订单交互，而没有任何其他权限。
- 在资源组级别分配一个角色，这样，用户便有权访问资源组中的所有 Data Box 订单。

有关 RBAC 建议用法的详细信息，请参阅 [RBAC 的最佳做法](../role-based-access-control/overview.md#best-practice-for-using-rbac)。

## <a name="track-the-order"></a>跟踪订单

可以通过 Azure 门户和承运商网站跟踪订单。 随时可以使用以下机制跟踪 Data Box 订单：

- 若要在设备已抵达 Azure 数据中心或你的所在地时跟踪订单，请在 Azure 门户中转到你的 Data Box 订单并选择“概述”。

    ![查看订单状态和跟踪号](media/data-box-logs/overview-view-status-1.png)

- 若要在设备运输过程中跟踪订单，请转到所在区域的承运商网站，例如，美国的 UPS 网站。 提供与订单关联的跟踪号。
- Data Box 还会根据创建订单时提供的电子邮件，随时发送电子邮件通知来告知订单状态的变化。 有关所有 Data Box 订单状态的列表，请参阅[查看订单状态](data-box-portal-admin.md#view-order-status)。 若要更改与订单关联的通知设置，请参阅[编辑通知详细信息](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安装期间查询活动日志

- 当 Data Box 抵达你的所在地时处于锁定状态。 可以使用 Azure 门户中根据订单提供的设备凭据。  

    如果已安装 Data Box，你可能需要知道谁访问过设备凭据。 若要了解谁访问过“设备凭据”边栏选项卡，可以查询活动日志。  涉及到访问“设备详细信息”>“凭据”边栏选项卡的任何操作都会作为 `ListCredentials` 操作记录到活动日志。

    ![查询活动日志](media/data-box-logs/query-activity-log-1.png)

- 每次登录 Data Box 都有实时的记录。 但是，此信息只会在成功完成订单之后才显示在[审核日志](#audit-logs)中。

## <a name="view-error-log-during-data-copy"></a>查看将数据复制期间的错误日志

将数据复制到 Data Box 或 Data Box Heavy 期间，如果所复制的数据出现任何问题，则会生成错误文件。

### <a name="errorxml-file"></a>Error.xml 文件

请确保复制作业已完成且未出错。 如果复制过程中出错，请从“连接和复制”页下载日志。 ****  

- 如果将未经 512 字节对齐的文件复制到 Data Box 上的托管磁盘文件夹，该文件不会作为页 Blob 上传到临时存储帐户。 日志中会出现错误。 请删除该文件，并复制经过 512 字节对齐的文件。
- 如果复制了 VHDX、动态 VHD 或差异 VHD（不支持这些文件），将在日志中看到错误。

下面是复制到托管磁盘时出现的不同错误的 *error.xml* 示例。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

下面是复制到页 Blob 时出现的不同错误的 *error.xml* 示例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


下面是复制到块 Blob 时出现的不同错误的 *error.xml* 示例。

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

下面是复制到 Azure 文件时出现的不同错误的 *error.xml* 示例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

如果出现上述任一情况，请先解决这些错误，然后继续下一步。 有关通过 SMB 或 NFS 协议将数据复制到 Data Box 期间出现的错误的详细信息，请转到[排查 Data Box 和 Data Box Heavy 问题](data-box-troubleshoot.md)。 有关通过 REST 将数据复制到 Data Box 期间出现的错误的信息，请转到[排查 Data Box Blob 存储问题](data-box-troubleshoot-rest.md)。

## <a name="inspect-bom-during-prepare-to-ship"></a>在准备交付期间检查 BOM

在准备交付期间，会创建名为物料清单 (BOM) 或清单文件的文件列表。

- 使用此文件检查已复制到 Data Box 的文件的实际名称和数目。
- 使用此文件检查文件的实际大小。
- 检查 *crc64* 是否对应于某个非零字符串。 <!--A null value for crc64 indicates that there was a reparse point error)-->

有关准备交付期间出现的错误的详细信息，请转到[排查 Data Box 和 Data Box Heavy 问题](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>BOM 或清单文件

BOM 或清单文件包含复制到 Data Box 设备的所有文件的列表。 BOM 文件包含文件名和相应的大小，以及校验和。 系统将为块 Blob、页 Blob，Azure 文件、通过 REST API 执行的复制，以及复制到 Data Box 上的托管磁盘的操作创建单独的 BOM 文件。 在准备交付期间，可以从设备的本地 Web UI 下载 BOM 文件。

这些文件还会保存在 Data Box 设备上，并会上传到 Azure 数据中心内的关联存储帐户中。

### <a name="bom-file-format"></a>BOM 文件格式

BOM 或清单文件采用以下常规格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

下面是将数据复制到 Data Box 上的块 Blob 共享时生成的清单示例。

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM 或清单文件还会复制到 Azure 存储帐户。 可以使用 BOM 或清单文件来确认上传到 Azure 文件是否与复制到 Data Box 的数据相匹配。

## <a name="review-copy-log-during-upload-to-azure"></a>查看上传到 Azure 期间的复制日志

在将数据上传到 Azure 的过程中, 会创建一个复制日志。

### <a name="copy-log"></a>复制日志

对于每个处理的订单, Data Box 服务在关联的存储帐户中创建复制日志。 复制日志包含已上传的文件总数以及从 Data Box 到你的 Azure 存储帐户的数据复制期间误码的文件数。

在上传到 Azure 期间，会执行循环冗余检查 (CRC) 计算。 系统会比较从数据复制开始到完成数据上传为止的 CRC。 如果 CRC 不匹配，则表示相应的文件无法上传。

默认情况下，日志将写入一个名为  `copylog` 的容器中。 使用以下命名约定存储日志：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`。

复制日志路径也会显示在门户的 "**概述**" 边栏选项卡中。

![完成时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>上传成功完成 

下面的示例描述了成功完成的 Data Box 上载的复制日志的常规格式:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>上传已完成, 但出现错误 

上传到 Azure 的过程也有可能出现这种状态：完成但出错。

![已完成但有错误时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-2.png)

下面是一个复制日志的示例, 其中上载已完成, 但出现错误:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>上传已完成, 但出现警告

如果你的数据具有不符合 Azure 命名约定的容器/blob/文件名, 并已将这些名称修改为将数据上载到 Azure, 则上传到 Azure 完成时会出现警告。

![已完成但出现警告时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-3.png)

下面是复制日志的示例, 其中不符合 Azure 命名约定的容器在数据上传到 Azure 的过程中已重命名。

容器的新唯一名称采用 `DataBox-GUID` 格式，容器的数据将放入已重命名的新容器。 复制日志为容器指定旧的和新的容器名称。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

下面举例说明了在将数据上传到 Azure 的过程中, 不符合 Azure 命名约定的 blob 或文件。 新的 Blob 或文件名称已转换为容器相对路径的 SHA256 摘要，并已根据目标类型上传到路径。 目标可以是块 Blob、页 Blob 或 Azure 文件。

`copylog` 指定旧的和新的 Blob 或文件名称，以及 Azure 中的路径。

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>擦除数据后获取监管日志链

根据 NIST SP 800-88 修订版 1 准则从 Data Box 磁盘中擦除数据后，会提供监管日志链。 这些日志包括审核日志和订单历史记录。 BOM 或清单文件也会随审核日志一起复制。

### <a name="audit-logs"></a>审核日志

审核日志包含有关如何在 Data Box 或 Data Box Heavy 在 Azure 数据中心外进行开机和访问共享的信息。 这些日志位于：`storage-account/azuredatabox-chainofcustodylogs`

下面是 Data Box 中的审核日志示例：

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>下载订单历史记录

Azure 门户提供订单历史记录。 如果订单处理和设备清理（从磁盘中擦除数据）已完成，请转到设备订单，然后导航到“订单详细信息”。此时会看到“下载订单历史记录”选项。 ****   有关详细信息，请参阅[下载订单历史记录](data-box-portal-admin.md#download-order-history)。

滚动浏览订单历史记录时，可以看到：

- 设备的承运商跟踪信息。
- 包含 *SecureErase* 活动的事件。 这些事件对应于磁盘上的数据擦除活动。
- Data Box 日志链接。 将显示*审核日志*、*复制日志*和*物料清单*文件的路径。

下面是 Azure 门户中的订单历史记录日志的示例：

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>后续步骤

- 了解如何[排查 Data Box 和 Data Box Heavy 上的问题](data-box-troubleshoot.md)。
