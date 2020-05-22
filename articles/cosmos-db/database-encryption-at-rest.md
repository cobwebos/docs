---
title: Azure Cosmos DB 中的静态加密
description: 了解 Azure Cosmos DB 如何提供静态数据加密及其实现方式。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: 541c6f4a6c728844524af794f5e2063f4e352cce
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592128"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Azure Cosmos DB 中的数据加密 

“静态加密”这个短语通常指永久性存储设备（例如固态硬盘 (SSD) 和硬盘驱动器 (HDD)）上的数据的加密。 Cosmos DB 将其主要数据库存储在 SSD 上。 其媒体附件和备份存储在 Azure Blob 存储中，通常由 HDD 进行备份。 随着 Cosmos DB 静态加密功能的发布，所有数据库、媒体附件以及备份都是加密的。 现在无论是在传输过程中（通过网络）还是处于静态（永久性存储），数据都处于加密状态，采用端到端的加密。

Azure Cosmos DB 属于 PaaS 服务，非常便于使用。 存储在 Azure Cosmos DB 中的所有用户数据无论是在传输过程中，还是处于静态时都处于加密状态，因此无需采取任何措施。 实现此目的的另一方式是，默认情况下，静态加密处于“开启”状态。 无法控制开启或关闭此功能。 Azure Cosmos DB 在帐户运行的所有区域中使用 AES-256 加密。 在提供此功能的同时，我们继续满足[可用性和性能 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)。 存储在 Azure Cosmos 帐户中的数据会自动使用由 Microsoft 管理的密钥（服务托管密钥）进行无缝加密。 或者，可以选择使用自己的密钥来添加另一层加密，如[客户托管的密钥](how-to-setup-cmk.md)一文中所述。

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
A:没有任何额外费用。

### <a name="q-who-manages-the-encryption-keys"></a>问：加密密钥由谁管理？
A:密钥由 Microsoft 管理。

### <a name="q-how-often-are-encryption-keys-rotated"></a>问：加密密钥多久轮换一次？
A:Microsoft 有一套关于加密密钥轮换的内部指导，Cosmos DB 按该指导执行。 具体指导方针不对外公布。 Microsoft 会对外公布[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/default.aspx)，其被视为内部指导的一部分，可为开发人员提供有用的最佳实践。

### <a name="q-can-i-use-my-own-encryption-keys"></a>问：我可以使用自己的加密密钥吗？
A:是的，此功能现在可用于新的 cosmos 帐户，这应在创建帐户时完成。 有关详细信息，请参阅[客户托管的密钥](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk)文档。

### <a name="q-what-regions-have-encryption-turned-on"></a>问：哪些区域已开启了此加密？
A:所有 Azure Cosmos DB 区域都已针对所有用户数据开启了此加密。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>问：加密是否会影响性能延迟和吞吐量 SLA？
A:现已为所有现有帐户和新帐户启用了静态加密，对性能 SLA 没有影响或更改。 可在 [Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 页上阅读详细信息，查看最新保证。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>问：本地模拟器是否支持静态加密？
A:模拟器是独立的开发/测试工具，不使用托管 Cosmos DB 服务使用的密钥管理服务。 我们的建议是在存储敏感模拟器测试数据的驱动器上启用 BitLocker。 [模拟器支持对默认数据目录进行更改](local-emulator.md)，也支持使用已知位置。

## <a name="next-steps"></a>后续步骤

* 可以选择使用自己的密钥来添加另一层加密，若要了解详细信息，请参阅[客户托管的密钥](how-to-setup-cmk.md)一文。
* 有关 Cosmos DB 安全性的概述以及最新改进，请参阅 [Azure Cosmos 数据库安全性](database-security.md)。
* 有关 Microsoft 认证的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。
