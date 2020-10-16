---
title: 使用受信任的服务异常对 Azure 存储空间进行索引器访问
titleSuffix: Azure Cognitive Search
description: 通过 Azure 认知搜索中的索引器启用数据访问，以便安全地存储在 Azure 存储中的数据。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101369"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>使用受信任的服务例外对 Azure 存储空间进行索引器访问 (Azure 认知搜索) 

Azure 认知搜索服务中访问 Azure 存储帐户中的数据的索引器可以利用 [受信任的服务异常](../storage/common/storage-network-security.md#exceptions) 功能来安全地访问数据。 此机制为不能 [使用 IP 防火墙规则授予索引器访问权限](search-indexer-howto-access-ip-restricted.md) 的客户提供了一种简单、安全和免费的替代方法，可用于访问存储帐户中的数据。

> [!NOTE]
> 支持通过受信任的服务例外访问存储帐户中的数据仅限于 Azure Blob 存储和 Azure Data Lake Gen2 存储。 不支持 Azure 表存储。

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>步骤1：使用托管标识配置连接

按照 [使用托管标识设置到 Azure 存储帐户的连接](search-howto-managed-identities-storage.md)中的说明进行操作。 完成后，你将使用 Azure Active Directory 作为受信任的服务注册你的搜索服务，并且你将在 Azure 存储中授予权限，以便为搜索标识提供访问数据或信息的特定权限。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步骤2：允许受信任的 Microsoft 服务访问存储帐户

在 Azure 门户中，导航到存储帐户的 " **防火墙和虚拟网络** " 选项卡。 确保选中 " **允许受信任的 Microsoft 服务访问此存储帐户** " 选项。 此选项只允许特定的搜索服务实例对存储帐户进行基于角色的访问， (强身份验证) 来访问存储帐户中的数据，即使该实例受 IP 防火墙规则保护，也是如此。

![受信任的服务异常](media\search-indexer-howto-secure-access\exception.png "受信任的服务异常")

索引器现在能够访问存储帐户中的数据，即使该帐户是通过 IP 防火墙规则保护的。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 存储索引器：

- [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
- [Azure 表索引器](search-howto-indexing-azure-tables.md)