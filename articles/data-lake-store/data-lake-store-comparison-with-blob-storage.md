---
title: "Azure Data Lake Store 与 Azure 存储 Blob 对比 | Microsoft Docs"
description: "Azure Data Lake Store 与 Azure 存储 Blob 对比"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: ab9c4bc6e2e68a3522bbc3fe23ea33760f03b620
ms.openlocfilehash: 10926263ee2657dc96fb1873733d349cf0956e92
ms.contentlocale: zh-cn
ms.lasthandoff: 01/05/2017


---
<a id="comparing-azure-data-lake-store-and-azure-blob-storage" class="xliff"></a>

# 比较 Azure Data Lake Store 与 Azure 存储 Blob
本文中的表总结了 Azure Data Lake Store 和 Azure 存储 Blob 在一些大数据处理的关键方面之间的差异。 Azure Blob 存储是一个常规用途和可扩展的对象存储，适用于多种存储方案。 Azure Data Lake Store 是一个超大规模存储库，优化用于大数据分析工作负荷。

|  | Azure Data Lake Store | Azure Blob 存储 |
| --- | --- | --- |
| 目的 |大数据分析工作负荷的优化存储 |用于多种存储方案的常规用途对象存储 |
| 用例 |Batch、交互式流分析和机器学习数据，例如日志文件、IoT 数据、点击流、大型数据集 |任何类型的文本或二进制数据，例如应用程序后端、备份数据、流式处理媒体存储和常规用途数据 |
| 关键概念 |Data Lake Store 帐户包含文件夹，而这些文件夹也会相应地将数据存储为文件 |存储帐户包含容器，而这些容器也会相应地包含 blob 形式的数据 |
| 结构 |分层文件系统 |具有平面命名空间的对象存储 |
| API |基于 HTTPS 的 REST API |基于 HTTP/HTTPS 的 REST API |
| 服务器端 API |[WebHDFS-compatible REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx)（兼容 WebHDFS 的 REST API） |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)（Azure blob 存储 REST API） |
| Hadoop 文件系统客户端 |是 |是 |
| 数据操作 - 身份验证 |基于 [Azure Active Directory 标识](../active-directory/active-directory-authentication-scenarios.md) |基于共享机密 - [帐户访问密钥](../storage/storage-create-storage-account.md#manage-your-storage-account)和[共享访问签名密钥](../storage/storage-dotnet-shared-access-signature-part-1.md)。 |
| 数据操作 - 身份验证协议 |OAuth 2.0。 调用必须包含 Azure Active Directory 发布的有效的 JWT（JSON Web 令牌） |基于哈希的消息身份验证代码 (HMAC)。 调用必须包含 Base64 编码的 SHA-256 哈希作为 HTTP 请求的一部分。 |
| 数据操作 - 授权 |POSIX 访问控制列表 (ACL)。  可设置基于 Azure Active Directory 标识的 ACL 为文件和文件夹级别。 |对于帐户级别授权 – 使用[帐户访问密钥](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>对于帐户、容器 或 blob 授权 - 使用[共享访问签名密钥](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| 数据操作 - 审核 |可用。 详细信息参见[此处](data-lake-store-diagnostic-logs.md)。 |可用 |
| 静态数据加密 |透明版服务器端 <ul><li>服务托管密钥</li><li>Azure KeyVault 中客户托管的密钥</li></ul> |<ul><li>透明版服务器端</li> <ul><li>服务托管密钥</li><li>Azure KeyVault 中客户托管的密钥（即将推出）</li></ul><li>客户端加密</li></ul> |
| 管理操作（例如“帐户创建”） |Azure 向帐户管理提供的[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md) (RBAC) |Azure 向帐户管理提供的[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md) (RBAC) |
| Developer SDK |.NET、Java、Python、Node.js |.Net、Java、Python、Node.js、C++、Ruby |
| 分析工作负荷性能 |并行分析工作负荷的优化性能。 高吞吐量和 IOPS。 |未进行分析工作负荷优化 |
| 大小限制 |无帐户大小、文件大小或文件数量限制 |[此处](../azure-subscription-service-limits.md#storage-limits)记录有具体限制 |
| 异地冗余 |本地冗余（一个 Azure 区域中数据的多个副本） |本地冗余 (LRS)、全局冗余 (GRS)、读取访问全局冗余 (RA-GRS)。 详细信息参见[此处](../storage/storage-redundancy.md) |
| 服务状态 |正式发布 |正式发布 |
| 区域可用性 |参见[此处](https://azure.microsoft.com/regions/#services) |参见[此处](https://azure.microsoft.com/regions/#services) |
| 价格 |参阅[定价](https://azure.microsoft.com/pricing/details/data-lake-store/) |参阅[定价](https://azure.microsoft.com/pricing/details/storage/) |

<a id="next-steps" class="xliff"></a>

### 后续步骤
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)
* [Data Lake Store 入门](data-lake-store-get-started-portal.md)


