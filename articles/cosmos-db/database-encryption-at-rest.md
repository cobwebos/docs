---
title: 数据库静态加密：Azure Cosmos DB | Microsoft Docs
description: 了解 Azure Cosmos DB 如何提供所有数据的默认加密。
services: cosmos-db
author: voellm
manager: kfile
documentationcenter: ''
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: 831fa2b6b9afb39bff715ef54cc41e11ee903e50
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure Cosmos DB 数据库静态加密

“静态加密”这个短语通常指永久性存储设备（例如固态硬盘 (SSD) 和硬盘驱动器 (HDD)）上的数据的加密。 Cosmos DB 将其主要数据库存储在 SSD 上。 其媒体附件和备份存储在 Azure Blob 存储中，通常由 HDD 进行备份。 随着 Cosmos DB 静态加密功能的发布，所有数据库、媒体附件以及备份都是加密的。 现在无论是在传输过程中（通过网络）还是处于静态（永久性存储），数据都处于加密状态，采用端到端的加密。

Cosmos DB 属于 PaaS 服务，非常便于使用。 存储在 Cosmos DB 中的所有用户数据无论是在传输过程中，还是处于静态时都处于加密状态，因此无需采取任何措施。 实现此目的的另一方式是，默认情况下，静态加密处于“开启”状态。 无法控制开启或关闭此功能。 在提供此功能的同时，我们继续满足[可用性和性能 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)。

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>实现针对 Azure Cosmos DB 的静态加密

静态加密是通过许多安全技术实现的，其中包括安全密钥存储系统、加密网络以及加密 API。 对数据进行解密和处理的系统必须与管理密钥的系统进行通信。 该图展示了加密数据的存储和密钥管理的不同之处。 

![设计图](./media/database-encryption-at-rest/design-diagram.png)

用户请求的基本流程如下：
- 用户数据库帐户准备就绪，通过向管理服务资源提供程序 (RP) 发出请求来检索存储密钥。
- 用户通过 HTTPS/安全传输创建与 Cosmos DB 的连接。 （SDK 对详细信息进行抽象化。）
- 用户通过之前创建的安全连接发送要存储的 JSON 文档。
- 为 JSON 文档编制索引，除非用户关闭了索引编制功能。
- 将 JSON 文档和索引数据都写入到安全存储中。
- 定期从安全存储中读取数据并将其备份到 Azure 加密 Blob 存储中。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>问：如果启用存储服务加密，需另付多少 Azure 存储费用？
答：没有任何额外费用。

### <a name="q-who-manages-the-encryption-keys"></a>问：加密密钥由谁管理？
答：密钥由 Microsoft 管理。

### <a name="q-how-often-are-encryption-keys-rotated"></a>问：加密密钥多久轮换一次？
答：Microsoft 有一套关于加密密钥轮换的内部指导，Cosmos DB 按该指导执行。 具体指导方针不对外公布。 Microsoft 会对外公布[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/default.aspx)，其被视为内部指导的一部分，可为开发人员提供有用的最佳实践。

### <a name="q-can-i-use-my-own-encryption-keys"></a>问：我可以使用自己的加密密钥吗？
答：Cosmos DB 是一项 PaaS 服务，我们一直在努力使该服务易于使用。 我们注意到此问题经常被作为一个与满足符合性要求（例如 PCI-DSS）相关的代理问题问起。 构建此功能的过程中，我们与符合性审核机构进行了合作，确保客户使用 Cosmos DB 可满足其要求，而无需自行管理密钥。

### <a name="q-what-regions-have-encryption-turned-on"></a>问：哪些区域已开启了此加密？
答：所有 Azure Cosmos DB 区域都已针对所有用户数据开启了此加密。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>问：加密是否会影响性能延迟和吞吐量 SLA？
答：现已为所有现有和新帐户启用了静态加密，对性能 SLA 没有影响或更改。 可在 [Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 页上阅读详细信息，查看最新保证。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>问：本地模拟器是否支持静态加密？
答：模拟器是独立的开发/测试工具，不使用托管 Cosmos DB 服务使用的密钥管理服务。 我们的建议是在存储敏感模拟器测试数据的驱动器上启用 BitLocker。 [模拟器支持对默认数据目录进行更改](local-emulator.md)，也支持使用已知位置。

## <a name="next-steps"></a>后续步骤

有关 Cosmos DB 安全性的概述以及最新改进，请参阅 [Azure Cosmos DB 数据库安全性](database-security.md)。
有关 Microsoft 认证的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。
