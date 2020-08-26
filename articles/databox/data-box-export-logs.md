---
title: 跟踪和记录 Azure Data Box，Azure Data Box Heavy 导出顺序的事件 |Microsoft Docs
description: 介绍如何在 Azure Data Box 的各个阶段跟踪和记录事件，并 Azure Data Box Heavy 导出顺序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 1d924e96cfc287060107f541e44980295eb24745
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494479"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Azure Data Box 和 Azure Data Box Heavy 导出订单的跟踪和事件日志记录

Data Box 或 Data Box Heavy 导出顺序完成以下步骤：订单、设置、数据复制、返回和数据擦除。 对于每个订单步骤，可以采取多种措施来控制对订单的访问、审核事件、跟踪订单，以及解释生成的各种日志。

本文详细介绍了可用于跟踪和审核 Data Box 或 Data Box Heavy 的出口订单的各种机制或工具。 本文中的信息同时适用于 Data Box 和 Data Box Heavy。 在后续部分，有关 Data Box 的任何参考信息也适用于 Data Box Heavy。

下表显示了 Data Box 导出订单步骤的摘要，以及在每个步骤中可用于跟踪和审核订单的工具。

| Data Box 出口订单阶段       | 用于跟踪和审核的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 创建订单               | [通过 RBAC 对订单设置访问控制](#set-up-access-control-on-the-order) <br> [按顺序启用详细日志](#enable-verbose-log-in-the-order)                                                    |
| 订单已处理            | 通过以下方式[跟踪订单](#track-the-order) <ul><li> Azure 门户 </li><li> 承运商网站 </li><li>电子邮件通知</ul> |
| 设置设备              | 在[活动日志](#query-activity-logs-during-setup)中记录的设备访问凭据              |
| 从设备复制数据        | [查看复制日志](#copy-log) <br> 在复制数据之前[查看详细日志](#verbose-log)            |
| 从设备中擦除数据   | [查看监护日志链](#get-chain-of-custody-logs-after-data-erasure)，包括审核日志和订单历史记录                |


## <a name="set-up-access-control-on-the-order"></a>针对订单设置访问控制

首次创建订单时，可以控制谁能够访问你的订单。 在不同范围中设置 Azure 角色，以控制对 Data Box 顺序的访问。 Azure 角色确定对操作子集的访问（读写、只读、读写）的类型。

可为 Azure Data Box 服务定义的两个角色：

- **Data Box 读取者** - 对订单拥有按范围定义的只读访问权限。 他们只能查看订单详细信息。 他们无法访问与存储帐户相关的任何其他详细信息，也无法编辑订单详细信息，例如地址等。
- **Data Box 参与者** - 仅当已对某个存储帐户拥有写入访问权限时，才能创建一个订单以将数据传输到给定的存储帐户。** 如果他们没有某个存储帐户的访问权限，则无法 Data Box 订单以将数据复制到该帐户。 此角色不会定义任何存储帐户相关的权限，也不授予对存储帐户的访问权限。  

若要限制对订单的访问，可以：

- 在订单级别分配一个角色。 用户只拥有角色定义的权限，只能与该特定的 Data Box 订单交互，而没有任何其他权限。
- 在资源组级别分配一个角色，这样，用户便有权访问资源组中的所有 Data Box 订单。

有关 RBAC 建议用法的详细信息，请参阅 [Azure RBAC 的最佳做法](../role-based-access-control/best-practices.md)。

## <a name="enable-verbose-log-in-the-order"></a>按顺序启用详细日志

在为 Data Box 放置出口订单时，您可以选择启用详细日志的集合。 下面是可以在其中启用详细日志的订单屏幕：

![选择导出选项](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

当你选择 "**包括详细日志**" 选项时，将在从 Azure 存储帐户复制数据时生成详细日志文件。 此日志包含已成功导出的所有文件的列表。      

有关导出顺序的详细信息，请参阅[为 Data Box 创建导出订单](data-box-deploy-export-ordered.md)

## <a name="track-the-order"></a>跟踪订单

可以通过 Azure 门户和承运商网站跟踪订单。 随时可以使用以下机制跟踪 Data Box 订单：

- 若要在设备已抵达 Azure 数据中心或你的所在地时跟踪订单，请在 Azure 门户中转到你的 Data Box 订单并选择“概述”。****

    ![查看订单状态和跟踪号](media/data-box-logs/overview-view-status-1.png)

- 若要在设备运输过程中跟踪订单，请转到所在区域的承运商网站，例如，美国的 UPS 网站。 提供与订单关联的跟踪号。
- Data Box 还会根据创建订单时提供的电子邮件，随时发送电子邮件通知来告知订单状态的变化。 有关所有 Data Box 订单状态的列表，请参阅[查看订单状态](data-box-portal-admin.md#view-order-status)。 若要更改与订单关联的通知设置，请参阅[编辑通知详细信息](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安装期间查询活动日志

- 当 Data Box 抵达你的所在地时处于锁定状态。 可以使用 Azure 门户中根据订单提供的设备凭据。  

    如果已安装 Data Box，你可能需要知道谁访问过设备凭据。 若要了解谁访问过“设备凭据”边栏选项卡，可以查询活动日志。****  涉及到访问“设备详细信息”>“凭据”边栏选项卡的任何操作都会作为 `ListCredentials` 操作记录到活动日志。****

    ![查询活动日志](media/data-box-logs/query-activity-log-1.png)

- 每次登录 Data Box 都有实时的记录。 但是，此信息仅在订单成功完成后在[保管审核日志链](#chain-of-custody-audit-logs)中可用。

## <a name="view-logs-during-data-copy"></a>在数据复制期间查看日志

在从 Data Box 复制数据之前，你可以下载并查看复制到 Data Box 的数据的*复制日志*和*详细日志*。 从 Azure 中的存储帐户将数据复制到 Data Box 时，将生成这些日志。 

### <a name="copy-log"></a>复制日志

从 Data Box 复制数据之前，请从 "**连接和复制**" 页面下载复制日志。

下面是在从 Azure 到 Data Box 设备的数据复制期间，*复制日志*的示例输出。

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
下面是*复制日志*出错并且某些文件未能从 Azure 复制时的示例输出。

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

你可以使用以下选项导出这些文件： 

- 你可以传输无法通过网络复制的文件。 
- 如果数据大小大于可用设备容量，则会发生部分复制，且未复制的所有文件都将在此日志中列出。 你可以将此日志用作输入 XML 来创建新的 Data Box 订单，然后复制这些文件。

### <a name="verbose-log"></a>详细日志

详细日志包含从 Azure 存储帐户成功导出的所有文件的列表。 该日志还包含文件大小和校验和计算。

详细日志的信息采用以下格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

下面是详细日志的示例输出。 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

详细日志还会复制到 Azure 存储帐户。 默认情况下，日志将写入一个名为 `copylog` 的容器中。 使用以下命名约定存储日志：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

复制日志路径也会显示在门户的“概述”边栏选项卡上。****

<!-- add a screenshot-->

你可以使用这些日志来验证从 Azure 复制的文件与复制到本地服务器的数据是否匹配。 

使用您的详细日志文件：

- 验证实际名称以及从 Data Box 复制的文件数。
- 验证文件的实际大小。
- 验证*crc64*是否与非零字符串相对应。 循环冗余检查（CRC）计算是在从 Azure 导出过程中完成的。 在将数据从 Data Box 复制到本地服务器后，可以从导出和 CRCs 进行比较。 CRC 不匹配指示相应的文件未能正确地复制。


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>擦除数据后获取监管日志链

根据 NIST SP 800-88 修订版 1 准则从 Data Box 磁盘中擦除数据后，会提供监管日志链。 这些日志包括保管链、审核日志和订单历史记录。 BOM 或清单文件也会随审核日志一起复制。

### <a name="chain-of-custody-audit-logs"></a>保管链审核日志

保管链审核日志包含有关在 Data Box 上打开和访问共享的信息，或在该资源超出 Azure 数据中心时 Data Box Heavy 的信息。 这些日志位于：`storage-account/azuredatabox-chainofcustodylogs`

下面是 Data Box 中的审核日志示例：

```output
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

Azure 门户提供订单历史记录。 如果订单处理和设备清理（从磁盘中擦除数据）已完成，请转到设备订单，然后导航到“订单详细信息”。**** “下载订单历史记录”选项可用。**** 有关详细信息，请参阅[下载订单历史记录](data-box-portal-admin.md#download-order-history)。

滚动浏览订单历史记录时，可以看到：

- 设备的承运商跟踪信息。
- 包含 *SecureErase* 活动的事件。 这些事件对应于磁盘上的数据擦除活动。
- Data Box 日志链接。 会显示审核日志、复制日志和 BOM 文件的路径。** ** **

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
