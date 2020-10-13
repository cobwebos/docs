---
title: 允许通过受信任的服务访问存储异常
titleSuffix: Azure Cognitive Search
description: 如何将说明如何设置可信服务例外来安全地访问存储帐户中的数据。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 4fbffaa7bc68bb32bd07b657f4b769e5af4302bf
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950018"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>通过受信任的服务异常安全访问存储帐户中的数据

访问存储帐户中数据的索引器可以利用 [受信任的服务异常](../storage/common/storage-network-security.md#exceptions) 功能来安全地访问数据。 此机制为无法 [通过 IP 防火墙规则授予索引器访问](search-indexer-howto-access-ip-restricted.md) 权限的客户提供了一种简单、安全和免费的替代方法来访问存储帐户中的数据。

> [!NOTE]
> 支持通过受信任的服务例外访问存储帐户中的数据仅限于 Azure Blob 存储和 Azure Data Lake Gen2 存储。 不支持 Azure 表存储。

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>步骤1：通过标识配置到存储帐户的连接

按照 [托管标识访问指南](search-howto-managed-identities-storage.md) 中概述的详细信息，将索引器配置为通过搜索服务的托管标识访问存储帐户。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步骤2：允许受信任的 Microsoft 服务访问存储帐户

在 Azure 门户中，导航到存储帐户的 "防火墙和虚拟网络" 选项卡。 确保选中 "允许受信任的 Microsoft 服务访问此存储帐户" 选项。 此选项只允许特定的搜索服务实例对存储帐户进行基于角色的访问， (强身份验证) 来访问存储帐户中的数据，即使该实例受 IP 防火墙规则保护，也是如此。

![受信任的服务异常](media\search-indexer-howto-secure-access\exception.png "受信任的服务异常")

索引器现在能够访问存储帐户中的数据，即使该帐户是通过 IP 防火墙规则保护的。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 存储索引器：

- [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
- [Azure 表索引器](search-howto-indexing-azure-tables.md)