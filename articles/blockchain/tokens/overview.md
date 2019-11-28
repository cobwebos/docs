---
title: 什么是 Azure Blockchain Tokens
description: Azure Blockchain Tokens 是用于颁发和管理令牌的平台即服务 (PaaS)。
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: c1c18298ba82775c9b10bb79c420704bd462e2b4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326090"
---
# <a name="what-is-azure-blockchain-tokens"></a>什么是 Azure 区块链令牌？

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens 是用于跨 Azure 中的区块链账本颁发和管理标准化令牌的平台即服务 (PaaS)。

使用 Azure Blockchain Tokens 可以通过预生成的令牌模板为区块链解决方案创建标准化的令牌。 还可以使用该服务撰写自己的令牌模板。 创建模板后，使用 Azure Blockchain Tokens 连接区块链并在其中颁发令牌。 颁发令牌后，可跨多个区块链网络管理令牌。

## <a name="templates"></a>模板

使用 Azure Blockchain Tokens 选择预生成的令牌模板，或创建自己的令牌模板。 Azure Blockchain Tokens 支持撰写令牌模板，使你能够基于支持的行为创建自己的令牌模板。 令牌模板可用于大多数区块链解决方案，因为它们将映射到最常用的令牌。 可以从模板着手，将其个性化，然后为解决方案部署令牌。

有关 Azure Blockchain Tokens 模板的详细信息，请参阅 [Azure Blockchain Tokens 模板](templates.md)。

## <a name="management"></a>管理

Azure Blockchain Tokens 提供 Azure 门户管理和 API 用于连接到现有的区块链网络。 目前，可以连接到 [Azure 区块链服务](../service/overview.md)或其他 Ethereum 系列区块链。

连接到一个或多个区块链网络后，可以使用 Azure Blockchain Tokens API 来颁发和管理要在区块链解决方案中使用的令牌。 使用 API 可以在业务应用程序和逻辑中集成令牌管理。 例如，可以使用 REST API 来管理令牌，而不是直接在区块链中管理令牌。

## <a name="blockchains-and-accounts"></a>区块链和帐户

Azure Blockchain Tokens 提供 Azure 门户管理和 API 用于在连接的区块链网络中创建新组和新的区块链帐户。 可以直接在连接的网络中创建新帐户，Azure Blockchain Tokens 会代你管理帐户私钥。 使用组可将多个网络中的不同区块链帐户组合到一起；还可以通过组来管理访问控制。

有关 Azure Blockchain Tokens 帐户管理的详细信息，请参阅 [Azure Blockchain Tokens 帐户管理](account-management.md)。

## <a name="token-taxonomy-framework"></a>令牌分类框架

Azure Blockchain Tokens 构建在基于标准的令牌分类框架 (TTF) 基础之上。 TTF 是[令牌分类计划](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) 令牌工作组创建的一系列可交付结果。 TTI 工作组为可跨所有主要账本（包括 Ethereum、Quorum、Corda 和 Hyperledger Fabric）应用的令牌及其行为定义业务分类。 该工作组的目标是创建一个框架用于从业务的立场标准化令牌的使用，以力求简化和普及化基于令牌的开发。 让行业在业务级别定义这些令牌及其行为，可以从操控令牌的业务逻辑中分离出令牌的详细实现环节。

## <a name="next-steps"></a>后续步骤

详细了解可用的 [Azure Blockchain Tokens 模板](templates.md)。
