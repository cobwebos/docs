---
title: 跟踪和记录 Azure Data Box、Azure Data Box Heavy 事件 |Microsoft Docs
description: 描述如何在 Azure Data Box 的各个阶段跟踪和记录事件，并 Azure Data Box Heavy 顺序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260015"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 和 Azure Data Box Heavy 的跟踪和事件日志记录

Data Box 或 Data Box Heavy 顺序完成以下步骤：订单、设置、数据复制、返回、上传到 Azure 并验证和数据擦除。 对应于订单中的每个步骤，您可以执行多个操作来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

下表显示了 Data Box 或 Data Box Heavy 顺序步骤的摘要，以及在每个步骤中可用于跟踪和审核订单的工具。

| Data Box 订单阶段       | 用于跟踪和审核的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 创建订单               | [通过 RBAC 设置对订单的访问控制](#set-up-access-control-on-the-order)                                                    |
| 已处理订单            | [跟踪订单](#track-the-order) <ul><li> Azure 门户 </li><li> 装运承运人网站 </li><li>电子邮件通知</ul> |
| 设置设备              | 在[活动日志](#query-activity-logs-during-setup)中登录的设备凭据访问                                              |
| 数据复制到设备        | 查看数据复制的[*错误 .xml*文件](#view-error-log-during-data-copy)                                                             |
| 准备交付            | [检查设备上的 BOM 文件](#inspect-bom-during-prepare-to-ship)或清单文件                                      |
| 数据上传到 Azure       | 查看在 Azure 数据中心内数据上传过程中[复制日志](#review-copy-log-during-upload-to-azure)是否出错                         |
| 从设备擦除数据   | 查看包含审核日志和订单历史记录[的保管链](#get-chain-of-custody-logs-after-data-erasure)                |

本文详细介绍了可用于跟踪和审核 Data Box 或 Data Box Heavy 顺序的各种机制或工具。 本文中的信息适用于、Data Box 和 Data Box Heavy。 在后续部分中，对 Data Box 的任何引用也适用于 Data Box Heavy。

## <a name="set-up-access-control-on-the-order"></a>设置对顺序的访问控制

首次创建订单时，可以控制谁可以访问你的订单。 在不同范围中设置基于角色的访问控制（RBAC）角色，以控制对 Data Box 顺序的访问。 RBAC 角色确定访问的类型-读写、只读、读写操作的子集。

可以为 Azure Data Box 服务定义两个角色：

- **Data Box 读取**者-对范围定义的顺序具有只读访问权限。 它们只能查看订单的详细信息。 它们不能访问与存储帐户相关的任何其他详细信息，也不能编辑订单详细信息，如地址等。
- **Data Box 参与者**-只能创建一个订单，以便将数据传输到给定的存储帐户（如果该帐户*已拥有存储帐户的写入访问权限*）。 如果他们无权访问某个存储帐户，则他们甚至无法创建 Data Box 的订单来向该帐户复制数据。 此角色不会定义任何与存储帐户相关的权限，也不会授予对存储帐户的访问权限。  

若要限制对订单的访问，可以执行以下操作：

- 在订单级别分配角色。 用户仅具有这些角色定义的权限，才能与特定 Data Box 顺序交互，而不是其他任何内容。
- 在资源组级别分配角色后，用户可以访问资源组中的所有 Data Box 订单。

有关建议 RBAC 使用的详细信息，请参阅[rbac 的最佳实践](../role-based-access-control/overview.md#best-practice-for-using-rbac)。

## <a name="track-the-order"></a>跟踪订单

你可以通过 Azure 门户和装运承运商网站来跟踪你的订单。 以下机制可随时跟踪 Data Box 顺序：

- 若要在设备位于 Azure 数据中心或本地时跟踪订单，请参阅 Azure 门户中的**Data Box 订单 > 概述**。

    ![查看订单状态和不跟踪](media/data-box-logs/overview-view-status-1.png)

- 若要在设备传输过程中跟踪订单，请前往区域运营商网站（例如，美国的 UPS 网站）。 提供与订单关联的跟踪编号。
- Data Box 还会在订单状态发生变化时，根据在创建订单时提供的电子邮件，随时发送电子邮件通知。 有关所有 Data Box 订单状态的列表，请参阅[查看订单状态](data-box-portal-admin.md#view-order-status)。 若要更改与订单关联的通知设置，请参阅[编辑通知详细信息](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安装过程中查询活动日志

- 你的 Data Box 会在你的本地进入锁定状态。 你可以使用 Azure 门户中提供的设备凭据作为你的订单。  

    设置 Data Box 后，你可能需要知道谁访问了设备凭据。 若要确定访问 "**设备凭据**" 边栏选项卡的人员，可以查询活动日志。  涉及访问**设备详细信息 > 凭据**"边栏选项卡的任何操作都作为 `ListCredentials` 操作记录到活动日志中。

    ![查询活动日志](media/data-box-logs/query-activity-log-1.png)

- 每次登录到 Data Box 都是实时记录的。 但是，仅在成功完成订单后，此信息才会出现在[审核日志](#audit-logs)中。

## <a name="view-error-log-during-data-copy"></a>在数据复制期间查看错误日志

在数据复制到 Data Box 或 Data Box Heavy 的过程中，如果要复制的数据有任何问题，则会生成错误文件。

### <a name="errorxml-file"></a>错误 .xml 文件

请确保复制作业已完成且没有错误。 如果复制过程中出错，请从“连接和复制”页下载日志。

- 如果将不是512字节的文件复制到 Data Box 上的托管磁盘文件夹中，则不会将该文件作为页 blob 上传到临时存储帐户。 日志中会出现错误。 请删除该文件，并复制经过 512 字节对齐的文件。
- 如果复制的是 VHDX、动态 VHD 或差异 VHD （不支持这些文件），则会在日志中看到一个错误。

下面是有关复制到托管磁盘时的错误的示例 *。*

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

下面是在复制到页 blob 时*出现的错误*的示例。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


下面是在复制到块 blob 时*出现的错误的 xml*示例。

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

下面是在复制到 Azure 文件时*出现的错误的 xml 示例。*

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

在上述每种情况下，请先解决这些错误，然后再继续下一步。 有关在将数据复制到通过 SMB 或 NFS 协议 Data Box 期间收到的错误的详细信息，请参阅[解决 Data Box 和 Data Box Heavy 问题](data-box-troubleshoot.md)。 有关在将数据复制到通过 REST Data Box 期间收到的错误的信息，请参阅[Data Box Blob 存储问题的疑难解答](data-box-troubleshoot-rest.md)。

## <a name="inspect-bom-during-prepare-to-ship"></a>准备装运期间检查 BOM

在准备交付期间，会创建一个名为物料清单（BOM）或清单文件的文件列表。

- 使用此文件来验证实际名称以及复制到 Data Box 的文件数。
- 使用此文件来验证文件的实际大小。
- 验证*crc64*是否与非零字符串相对应。 <!--A null value for crc64 indicates that there was a reparse point error)-->

有关准备装运期间收到的错误的详细信息，请参阅[解决 Data Box 和 Data Box Heavy 问题](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>BOM 或清单文件

BOM 或清单文件包含已复制到 Data Box 设备的所有文件的列表。 BOM 文件具有文件名称以及相应的大小和校验和。 将为块 blob、页 blob、Azure 文件创建单独的 BOM 文件，以便通过 REST Api 进行复制，以及复制到 Data Box 上的托管磁盘。 你可以从设备的本地 web UI 中下载 BOM 文件，并准备交付。

这些文件也驻留在 Data Box 设备上，并上传到 Azure 数据中心内关联的存储帐户。

### <a name="bom-file-format"></a>BOM 文件格式

BOM 或清单文件具有以下常规格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

下面是在将数据复制到 Data Box 上的块 blob 共享时生成的清单的示例。

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

BOM 或清单文件也会复制到 Azure 存储帐户。 您可以使用 BOM 或清单文件来验证上传到 Azure 的文件与复制到 Data Box 的数据是否匹配。

## <a name="review-copy-log-during-upload-to-azure"></a>在上传到 Azure 的过程中查看复制日志

在将数据上传到 Azure 的过程中，会创建一个复制日志。

### <a name="copy-log"></a>复制日志

对于每个处理的订单，Data Box 服务在关联的存储帐户中创建复制日志。 复制日志包含已上传的文件总数以及从 Data Box 到你的 Azure 存储帐户的数据复制期间误码的文件数。

循环冗余检查（CRC）计算在上传到 Azure 的过程中完成。 在比较数据副本和数据上传后的 CRCs。 CRC 不匹配指示上载相应的文件失败。

默认情况下，日志将写入一个名为 `copylog` 的容器中。 日志的存储方式如下：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml` 列中的一个值匹配。

复制日志路径也会显示在门户的 "**概述**" 边栏选项卡中。

![完成时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>上传成功完成 

下面的示例描述了成功完成的 Data Box 上载的复制日志的常规格式：

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>上传已完成，但出现错误 

上载到 Azure 还可能会出现错误。

![已完成但有错误时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-2.png)

下面是一个复制日志的示例，其中上载已完成，但出现错误：

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
### <a name="upload-completed-with-warnings"></a>上传已完成，但出现警告

如果你的数据具有不符合 Azure 命名约定的容器/blob/文件名，并已将这些名称修改为将数据上载到 Azure，则上传到 Azure 完成时会出现警告。

![已完成但出现警告时在概述边栏选项卡中复制日志的路径](media/data-box-logs/copy-log-path-3.png)

下面是复制日志的示例，其中不符合 Azure 命名约定的容器在数据上传到 Azure 的过程中已重命名。

容器的新唯一名称采用格式 `DataBox-GUID`，容器的数据将被放入新的重命名容器。 复制日志为容器指定旧的和新的容器名称。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

下面举例说明了在将数据上传到 Azure 的过程中，不符合 Azure 命名约定的 blob 或文件。 新的 blob 或文件名称将转换为容器的相对路径的 SHA256 摘要，并将其上载到基于目标类型的路径。 目标可以是块 blob、页 blob 或 Azure 文件。

`copylog` 指定旧的和新的 blob 或文件名以及 Azure 中的路径。

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>在数据擦除后获取保管日志链

按照 NIST SP 800-88 修订版1指导原则从 Data Box 磁盘中删除数据后，可以使用保管日志链。 这些日志包括审核日志和订单历史记录。 BOM 或清单文件也会连同审核日志一起复制。

### <a name="audit-logs"></a>审核日志

审核日志包含有关如何在 Data Box 或 Data Box Heavy 在 Azure 数据中心外进行开机和访问共享的信息。 这些日志位于： `storage-account/azuredatabox-chainofcustodylogs`

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

Azure 门户中提供了订单历史记录。 如果订单已完成并且设备清除（磁盘中的数据擦除）已完成，则转到你的设备顺序并导航到 "**订单详细信息**"。 “下载订单历史记录”选项可用。 有关详细信息，请参阅[下载订单历史记录](data-box-portal-admin.md#download-order-history)。

如果滚动到订单历史记录，将看到：

- 设备的运营商跟踪信息。
- 具有*SecureErase*活动的事件。 这些事件对应于磁盘上的数据擦除。
- Data Box 日志链接。 将显示*审核日志*、*复制日志*和*物料清单*文件的路径。

下面是 Azure 门户的订单历史记录日志的示例：

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

- 了解如何[解决 Data Box 和 Data Box Heavy 上的问题](data-box-troubleshoot.md)。
