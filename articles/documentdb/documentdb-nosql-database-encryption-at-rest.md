---
title: "数据库静态加密 - Azure Cosmos DB | Microsoft Docs"
description: "了解 Azure Cosmos DB 如何提供所有数据的默认加密。"
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure Cosmos DB 数据库静态加密

“静态加密”这个短语通常指非易失性存储设备（例如固态硬盘 (SSD) 和硬盘 (HDD)）上的数据的加密。  Azure Cosmos DB 将其主数据库存储在 SSD 中，将其媒体附件和备份存储在通常由 HDD 支持的 Azure Blob 中。  随着 Azure Cosmos DB 静态加密功能的发布，现在，所有数据库、媒体附件以及备份都是加密的。  这意味着现在你的数据在传输中（通过网络）以及在静态时（非易失性存储）都处于加密状态，从而为你提供了端到端加密。

Azure Cosmos DB 是一项 PaaS 服务，我们一直在努力使其变得易于使用。  秉承着这一理念，Azure Cosmos DB 中存储的所有用户数据在静态时和传输中都处于加密状态，不需要用户执行任何操作。  实现这一理念的另一方式是默认“开启”静态加密。  没有控件可用来开启或关闭此功能，我们提供了此功能，同时继续满足我们的[可用性和性能 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)。

## <a name="how-does-encryption-at-rest-work"></a>静态加密的工作原理是什么？

静态加密是使用许多安全技术实现的，这包括安全的密钥存储系统、加密的网络以及加密 API。  下图展示了加密数据的存储和密钥的管理是如何隔离的。  对数据进行解密和处理的系统必须与管理密钥的系统进行通信。

![设计图](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

用户请求的基本流程如下所述：
- 用户数据库帐户准备就绪，通过向管理服务资源提供程序 (RP) 发出请求来检索存储密钥。
- 用户通过 HTTPS/安全传输创建与 Azure Cosmos DB 的连接（SDK 对详细信息进行抽象化）。
- 用户通过之前创建的安全连接发送要存储的 JSON 文档。
- 为 JSON 文档编制索引，除非已关闭了索引编制功能。
- 将 JSON 文档和索引数据都写入到安全存储中。
- 定期从安全存储中读取数据并将其备份到 Azure 加密 Blob 存储中。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>问：如果启用 SSE，Azure 存储的费用将会高多少？
答：没有任何额外费用。

### <a name="q-who-manages-the-encryption-keys"></a>问：加密密钥由谁管理？
答：密钥由 Microsoft 管理。

### <a name="q-how-often-are-encryption-keys-rotated"></a>问：加密密钥多久轮换一次？
答：Microsoft 具有一组内部指南，DocumentDB 遵循该指南。  虽然未发布具体指南，但 Microsoft 发布了安全开发生命周期（又称为 SDL） [https://www.microsoft.com/sdl/default.aspx]，它被视为内部指南的子集，并具有适用于开发人员的最佳做法。

### <a name="q-can-i-use-my-own-encryption-keys"></a>问：我可以使用自己的加密密钥吗？
答：Azure Cosmos DB 是一项 PaaS 服务，我们一直在努力使该服务易于使用。  我们注意到此问题经常被作为一个与满足合规性（例如 PCI-DSS）相关的代理问题问起。  构建此功能的过程中，我们与符合性审核机构合作来确保使用 Azure Cosmos DB 可满足客户要求，而不需要客户自行管理密钥。
因此，我们当前没有向用户提供选项来使他们自己负责密钥管理。


### <a name="q-what-regions-have-encryption-turned-on"></a>问：哪些区域已开启了此加密？
答：所有 Azure Cosmos DB 区域都已针对所有用户数据开启了此加密。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>问：加密是否会影响性能延迟和吞吐量 SLA？
答：现已为所有现有和新帐户启用了静态加密，对性能 SLA 没有影响或更改。  可以在[性能 SLA 页](https://azure.microsoft.com/support/legal/sla/documentdb)上阅读详细信息，查看最新保证。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>问：本地模拟器是否支持静态加密？
答：模拟器是独立的开发/测试工具，不使用托管 DocumentDB 服务使用的密钥管理服务。 我们的建议是在存储敏感模拟器测试数据的驱动器上启用 BitLocker。 （模拟器支持更改默认数据目录）[https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator] 以及使用已知位置。

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB 安全性的概述以及最新改进，请参阅 [Azure Cosmos DB 数据库安全性](documentdb-nosql-database-security.md)。

有关 Microsoft 认证的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center/)。

