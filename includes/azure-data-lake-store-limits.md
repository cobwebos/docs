---
title: include 文件
description: include 文件
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665581"
---
Azure Data Lake Storage Gen2 不是专用的服务或存储帐户类型。 它是最新发布的专用于大数据分析的功能。  这些功能在常规用途 v2 或 BlockBlobStorage 存储帐户中提供，你可以通过启用该帐户的“分层命名空间”功能来获取它们。 有关缩放目标的信息，请参阅以下文章。 

- [Blob 存储的缩放目标](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage)。
- [标准存储帐户的缩放目标](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts)。

Azure Data Lake Storage Gen1 是一项专用服务。 它是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 你可以使用 Data Lake Storage Gen1 在单个位置捕获任何大小、类型和引入速度的数据，以用于操作性和探索性分析。 可在 Data Lake Storage Gen1 帐户中存储的数据量没有限制。

| **资源** | **限制** | **注释** |
| --- | --- | --- |
| 每个区域每个订阅的 Data Lake Storage Gen1 帐户的最大数量 |10 | 若要请求增加此限制，请与支持人员联系。 |
| 每个文件或文件夹的最大访问 ACL 数 |32 | 这是硬性限制。 使用组来管理访问权限，减少条目数量。 |
| 每个文件或文件夹的最大默认 ACL 数 |32 | 这是硬性限制。 使用组来管理访问权限，减少条目数量。 |
