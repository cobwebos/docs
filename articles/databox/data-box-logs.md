---
title: 跟踪和记录 Azure Data Box 事件 |Microsoft Docs
description: 介绍如何跟踪和日志事件在不同的 Azure Data Box 订单阶段。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 7a6adc72c1dfbe67311ae2ca98d5b07dfab41719
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806501"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box"></a>跟踪和 Azure Data Box 的事件日志记录

Data Box 订单将完成以下步骤： 订单，设置，数据将复制、 返回、 上传到 Azure 和验证，并且数据擦除。 对应于每个步骤的顺序，可以执行多个操作来控制对顺序的访问、 审核事件、 跟踪订单，并解释生成的各种日志。

下表显示的数据框顺序步骤和工具可跟踪和审核顺序在每个步骤的摘要。

| 数据框顺序阶段       | 工具，用于跟踪和审核                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 创建订单               | [设置的顺序通过 RBAC 对访问控制](#set-up-access-control-on-the-order)                                                    |
| 处理顺序            | [跟踪的顺序](#track-the-order)通过 <ul><li> Azure 门户 </li><li> 传送快递商网站 </li><li>电子邮件通知</ul> |
| 设置设备              | 设备凭据登录访问[活动日志](#query-activity-logs-during-setup)                                              |
| 数据复制到设备        | [视图*error.xml*文件](#view-error-log-during-data-copy-to-data-box)数据副本                                                             |
| 准备交付            | [检查物料清单文件](#inspect-bom-during-prepare-to-ship)或在设备上的清单文件                                      |
| 数据上传到 Azure       | [审阅*拷贝*](#review-copy-log-during-upload-to-azure)数据期间发生的错误上传在 Azure 数据中心                         |
| 从设备数据擦除   | [查看托管链日志链](#get-chain-of-custody-logs-after-data-erasure)包括审核日志和订单历史记录                                                   |

本文详细介绍各种机制或工具可用来跟踪和审核 Data Box 订单。

## <a name="set-up-access-control-on-the-order"></a>设置访问控制的顺序

您可以控制谁可以访问你的订单，订单首次创建时。 设置基于角色的访问控制 (RBAC) 角色的不同范围内来控制对 Data Box 订单的访问权限。 RBAC 角色确定的访问权限 – 读写、 只读、 读写操作的一个子集的类型。

可以定义两个 Data Box 角色是：

- **数据框读取器**-有由作用域定义到订单的只读访问权限。 他们只能查看订单的详细信息。 它们不能访问任何其他相关存储帐户的详细信息或编辑订单详细信息，如地址，等等。
- **数据框参与者**-只能创建一个订单以将数据传输到给定的存储帐户*如果他们已有对存储帐户的写入访问*。 如果它们没有存储帐户的访问权限，他们甚至不能创建一个 Data Box 订单将数据复制到该帐户。 此角色不定义任何存储帐户与存储帐户相关的权限，也不授予访问权限。  

若要限制对订单的访问，您可以：

- 分配顺序级别的角色。 用户只具有这些权限由与该特定 Data Box 订单和其他任何内容进行交互的角色定义。
- 在资源组级别分配角色，用户有权访问资源组中的所有 Data Box 订单。

建议的 RBAC 用法的详细信息，请参阅[RBAC 的最佳实践](../role-based-access-control/overview.md#best-practice-for-using-rbac)。

## <a name="track-the-order"></a>跟踪订单

你可以跟踪你的订单通过 Azure 门户和传送的快递商网站。 以下机制均已到位，以在任何时间跟踪 Data Box 订单：

- 若要跟踪的顺序，在 Azure 数据中心或你的本地设备时，请转到您**Data Box 订单 > 概述**在 Azure 门户中。

    ![查看订单状态和跟踪号](media/data-box-logs/overview-view-status-1.png)

- 若要跟踪的顺序，而该设备是在传输过程中，转到区域的快递商网站，例如，UPS 在我们的网站。 提供与你的订单关联的跟踪号。
- 数据框还会发送电子邮件通知，只要订单状态更改，根据创建顺序时提供的电子邮件。 有关所有 Data Box 订单状态的列表，请参阅[查看订单状态](data-box-portal-admin.md#view-order-status)。 若要更改与该订单关联的通知设置，请参阅[编辑通知详细信息](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安装过程中查询活动日志

- Data Box 将到达您的内部处于锁定状态。 针对你的订单，可以使用 Azure 门户中可用的设备凭据。  

    如果数据中设置了，可能需要知道这些用户都可以访问的设备凭据。 若要找出谁访问**设备凭据**边栏选项卡中，您可以查询活动日志。  任何操作都涉及到访问**设备详细信息 > 凭据**边栏选项卡中登录到活动日志作为`ListCredentials`操作。

    ![查询活动日志](media/data-box-logs/query-activity-log-1.png)

- 每个登录到 Data Box 是记录的实际时间。 但是，此信息将仅出现在[审核日志](#audit-logs)顺序成功完成后。

## <a name="view-error-log-during-data-copy-to-data-box"></a>数据复制到 Data Box 期间查看错误日志

期间的数据复制到 Data Box，如果有任何问题与要复制的数据，则会生成错误文件。

### <a name="errorxml-file"></a>Error.xml 文件

请确保复制作业已完成且未出错。 如果在复制过程中有错误，下载生成的日志 **连接和复制** 页。

- 如果复制不是 512 个字节对齐到 Data Box 上的托管的磁盘文件夹的文件，该文件不是作为到临时存储帐户的页 blob 上载。 日志中会出现错误。 请删除该文件，并复制经过 512 字节对齐的文件。
- 如果您复制 VHDX 或动态 VHD 或差异 VHD （不支持这些文件），将看到在日志中的错误。

下面是一个示例的*error.xml*为不同的错误时将复制到托管磁盘。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

下面是一个示例的*error.xml*为不同的错误时将复制到页 blob。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


下面是一个示例的*error.xml*为不同的错误时将复制到块 blob。

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

下面是一个示例的*error.xml*为不同的错误时将复制到 Azure 文件。

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

在每个上述情况下，在继续进行下一步之前解决这些错误。 在数据复制到 Data Box 通过 SMB 或 NFS 协议期间收到的错误的详细信息，请转到[问题进行故障排除数据框](data-box-troubleshoot.md)。 在数据复制到 Data Box 通过 REST 期间收到的错误的信息，请转到[进行故障排除数据框 Blob 存储空间问题](data-box-troubleshoot-rest.md)。

## <a name="inspect-bom-during-prepare-to-ship"></a>检查 BOM 期间准备交付

在准备交付，名为创建的物料清单 (BOM) 或清单文件的文件的列表。

- 使用此文件来验证实际的名称和已复制到 Data Box 的文件数。
- 使用此文件来验证文件的实际大小。
- 确认*crc64*对应于一个非零值字符串。 <!--A null value for crc64 indicates that there was a reparse point error)-->

有关详细信息过程中接收到的错误准备交付，请转到[问题进行故障排除数据框](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>物料清单或清单文件

物料清单或清单文件包含被复制到 Data Box 设备的所有文件的列表。 物料清单文件具有文件的名称和相应的大小，以及校验和。 为块 blob、 页 blob，Azure 文件，请通过 REST Api 中，复制和复制到托管磁盘在数据中创建一个单独的物料清单文件。 在准备交付过程，可以从本地 web UI 的设备下载物料清单文件。

这些文件也驻留在 Data Box 设备，并上传到 Azure 数据中心中的关联的存储帐户。

### <a name="bom-file-format"></a>物料清单文件格式

物料清单或清单文件具有以下常规格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

下面是清单的当数据已复制到 Data Box 上的块 blob 共享时，生成的示例。

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

物料清单或清单文件也复制到 Azure 存储帐户中。 您可以使用 BOM 或清单文件，以验证上传到 Azure 文件与已复制到数据中的数据匹配。

## <a name="review-copy-log-during-upload-to-azure"></a>上传到 Azure 期间查看复制日志

在数据上传到 Azure 期间*copylog*创建。

### <a name="copylog"></a>Copylog

对于处理每个订单，Data Box 服务创建*copylog*关联的存储帐户中。 *Copylog*已上传的文件总数和从数据框中将复制到 Azure 存储帐户的出错期间数据文件数。

循环冗余检查 (CRC) 计算是在上传到 Azure 完成的。 从数据复制和之后数据上传进行比较的 Crc。 CRC 不匹配表示相应的文件无法上传。

默认情况下，日志写入名为 copylog 的容器。 这些日志存储使用以下命名约定：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`。

Copylog 路径也会显示在**概述**门户边栏选项卡。

![在概述边栏选项卡完成 copylog 路径](media/data-box-logs/copy-log-path-1.png)

为数据框将其上传已成功完成，下面的示例说明了 copylog 文件的一般格式：

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

上的传到 Azure 可能还完成但出现错误。

![在概述边栏选项卡时已完成，但错误 copylog 路径](media/data-box-logs/copy-log-path-2.png)

下面是上传完成，发生错误的 copylog 示例：

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


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>在数据擦除后获取监管日志的链

从根据 NIST SP 800-88 修订版 1 指导原则的 Data Box 磁盘擦除数据后，即可用的监管日志链。 这些日志包括审核日志和订单历史记录。 物料清单或清单文件也复制使用审核日志中。

### <a name="audit-logs"></a>审核日志

审核日志包含有关电源上的和 Azure 数据中心之外时共享上 Data Box 的访问权限。 这些日志位于以下位置： `storage-account/azuredatabox-chainofcustodylogs`

下面是从数据中的审核日志的一个示例：

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

Azure 门户中提供了订单历史记录。 如果订单处理完成，并且设备清理 （从磁盘数据擦除） 已完成，然后转到 **Data Box 订单 > 订单详细信息**。 **下载订单历史记录** 选项才可用。 有关详细信息，请参阅[下载订单历史记录](data-box-portal-admin.md#download-order-history)。

如果滚动浏览订单历史记录时，将看到：

- 承运人跟踪你的设备的信息。
- 使用事件*SecureErase*活动。 这些事件对应于磁盘上的数据清除。
- 数据框日志链接。 路径*审核日志*，*拷贝*，并*BOM*文件会显示。

下面是从 Azure 门户的订单历史记录日志的示例：

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-pinto                              
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
Copy Logs Path       : databoxcopylog/gus-pinto_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>后续步骤

- 了解如何[对 Data Box 上的问题进行故障排除](data-box-troubleshoot.md)。
