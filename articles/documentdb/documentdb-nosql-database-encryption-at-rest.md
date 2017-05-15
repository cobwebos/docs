---
title: "NoSQL 数据库静态加密 - Azure DocumentDB | Microsoft Docs"
description: "了解 Azure DocumentDB 如何为所有 NoSQL 数据提供默认加密。"
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>DocumentDB NoSQL 数据库静态加密

“静态加密”这个短语通常指非易失性存储设备（例如固态硬盘 (SSD) 和硬盘 (HDD)）上的数据的加密。  DocumentDB 将其主数据库存储在 SSD 中，将其媒体附件和备份存储在通常由 HDD 支持的 Azure Blob 中。  随着 DocumentDB 的静态加密功能的发布，现在，所有数据库、媒体附件以及备份都是加密的。  这意味着现在你的数据在传输中（通过网络）以及在静态时（非易失性存储）都处于加密状态，从而为你提供了端到端加密。

DocumentDB 是一项 PaaS 服务，我们一直在努力使其变得非常易于使用。  秉承着这一理念，我们已使 DocumentDB 中存储的所有用户数据在静态时和传输中都处于加密状态，不需要用户执行任何操作。  实现这一理念的另一方式是默认“开启”静态加密。  没有控件可用来开启或关闭此功能，我们提供了此功能，同时继续满足我们的[可用性和性能 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)。

## <a name="how-does-encryption-at-rest-work"></a>静态加密的工作原理是什么？

静态加密是使用许多安全技术实现的，这包括安全的密钥存储系统、加密的网络以及加密 API。  下图展示了加密数据的存储和密钥的管理是如何隔离的。  对数据进行解密和处理的系统必须与管理密钥的系统进行通信。

![设计图](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

用户请求的基本流程如下所述：
- 用户数据库帐户准备就绪，通过向管理服务资源提供程序 (RP) 发出请求来检索存储密钥。
- 用户通过 HTTPS/安全传输创建与 DocumentDB 的连接（SDK 对详细信息进行抽象化）。
- 用户通过之前创建的安全连接发送要存储的 JSON 文档。
- 为 JSON 文档编制索引，除非已关闭了索引编制功能。
- 将 JSON 文档和索引数据都写入到安全存储中。
- 定期从安全存储中读取数据并将其备份到 Azure 加密 Blob 存储中。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>问：如果启用 SSE，Azure 存储的费用将会高多少？
答：没有任何额外费用。

### <a name="q-who-manages-the-encryption-keys"></a>问：加密密钥由谁管理？
答：密钥由 Microsoft 管理。

### <a name="q-can-i-use-my-own-encryption-keys"></a>问：我可以使用自己的加密密钥吗？
答：DocumentDB 是一项 PaaS 服务，我们一直在努力使该服务易于使用。  我们注意到此问题经常被作为一个与满足合规性（例如 PCI-DSS）相关的代理问题问起。  在构建此功能时，我们一直与合规审核机构合作来确保使用 DocumentDB 的客户满足其要求，不需要客户自己管理密钥。
因此，我们当前没有向用户提供选项来使他们自己负责密钥管理。

### <a name="q-what-regions-have-encryption-turned-on"></a>问：哪些区域已开启了此加密？
答：所有 DocumentDB 区域都已针对所有用户数据开启了此加密。

## <a name="next-steps"></a>后续步骤

有关 DocumentDB 安全性的概述以及最新改进，请参阅 [DocumentDB NoSQL 数据库安全性](documentdb-nosql-database-security.md)。

有关 Microsoft 认证的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center/)。

