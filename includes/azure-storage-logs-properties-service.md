---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6d90b4b68f1f9f4a0fcd20c1d371a32759449fab
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711154"
---
| properties | 说明 |
|:--- |:---|
|**accountName** | 存储帐户的名称。 例如：`mystorageaccount`。  |
|**requestUrl** | 所请求的 URL。 |
|**userAgentHeader** | **User-Agent** 标头值，带引号。 例如：`WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`。|
|**referrerHeader** | **Referrer** 标头值。 例如：`http://contoso.com/about.html`。|
|**clientRequestId** | 请求的 **x-ms-client-request-id** 标头值。 例如：`360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`。 |
|**etag** | 返回的对象的 ETag 标识符，带引号。 例如：`0x8D101F7E4B662C4`。  |
|**serverLatencyMs** | 执行所请求操作的总时间（以毫秒为单位）。 此值不包括网络延迟（读取传入请求和向请求者发送响应的时间）。 例如：`22`。 |
|**serviceType** | 与此请求关联的服务。 例如：`blob`、`table`、`files` 或 `queue`。 |
|**operationCount** | 请求中涉及的每个已记录操作的编号。 此计数从索引 `0` 开始。 某些请求需要多个操作。 大多数请求仅执行一个操作。 例如：`1`。 |
|**requestHeaderSize** | 请求标头的大小（以字节为单位）。 例如：`578`。 <br>如果请求失败，此值可能为空。 |
|**requestBodySize** | 存储服务读取的请求数据包的大小（以字节为单位）。 <br> 例如：`0`。 <br>如果请求失败，此值可能为空。  |
|**responseHeaderSize** | 响应标头的大小（以字节为单位）。 例如：`216`。 <br>如果请求失败，此值可能为空。  |
|**responseBodySize** | 存储服务写入的响应数据包的大小（以字节为单位）。 如果请求失败，此值可能为空。 例如：`216`。  |
|**requestMd5** | 请求中的 **Content-MD5** 标头或 **x-ms-content-md5** 标头的值。 此字段中指定的 MD5 哈希值表示请求中的内容。 例如：`788815fd0198be0d275ad329cafd1830`。 <br>此字段可以为空。  |
|**serverMd5** | 存储服务计算的 MD5 哈希的值。 例如：`3228b3cf1069a5489b298446321f8521`。 <br>此字段可以为空。  |
|**lastModifiedTime** | 返回的对象的上次修改时间 (LMT)。  例如：`Tuesday, 09-Aug-11 21:13:26 GMT`。 <br>对于可以返回多个对象的操作，此字段为空。 |
|**conditionsUsed** | 表示条件的键/值对的分号分隔列表。 条件可以是以下任意一种： <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 例如：`If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`。 |
|**contentLengthHeader** | 发送到存储服务的请求的 Content-Length 标头值。 如果请求成功，则此值等于 requestBodySize。 如果请求失败，则此值可能不等于 requestBodySize，也可能为空。 |
|**tlsVersion** | 请求在连接时使用的 TLS 版本。 例如：`TLS 1.2`。 |
|**smbTreeConnectID** | 在树连接时建立的服务器消息块 (SMB) **treeConnectId**。 例如： `0x3` |
|**smbPersistentHandleID** | SMB2 CREATE 请求在经历网络重新连接后会保留的持久性句柄 ID。  在 [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中称为 **SMB2_FILEID.Persistent**。 例如： `0x6003f` |
|**smbVolatileHandleID** | SMB2 CREATE 请求在网络重新连接时将回收的易失句柄 ID。  在 [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中称为 **SMB2_FILEID.Volatile**。 例如： `0xFFFFFFFF00000065` |
|**smbMessageID** | 连接相关 **MessageId**。 例如： `0x3b165` |
|**smbCreditsConsumed** | 请求消耗的流入量或流出量（以 64k 为单位）。 例如： `0x3` |
|**smbCommandDetail** | 有关此特定请求而不是常规类型请求的详细信息。 例如： `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 与文件或目录关联的 **FileId**。  大致类似于 NTFS FileId。 例如： `0x9223442405598953` |
|**smbSessionID** | 在建立会话时建立的 SMB2 **SessionId**。 例如： `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command** 中的值。 目前，这是一个 0 到 18（含）之间的数字。 例如： `0x6` |
|**smbCommandMinor** | **SmbCommandMajor** 的子类（如果适用）。 例如： `DirectoryCloseAndDelete` |