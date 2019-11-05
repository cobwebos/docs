---
title: 什么是 Azure 区块链令牌
description: Azure 区块链令牌是一种平台即服务（PaaS），用于令牌颁发和管理。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: d04c4202f89e58185465669c3da0f7d5e4d7b9bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518640"
---
# <a name="what-is-azure-blockchain-tokens"></a>什么是 Azure 区块链令牌？

[!INCLUDE [Preview note](./includes/preview.md)]

Azure 区块链令牌是一种平台即服务（PaaS），用于跨 Azure 中的区块链分类帐进行标准化令牌颁发和管理。

使用 Azure 区块链令牌，你可以使用预建的令牌模板为区块链解决方案创建标准化令牌。 你还可以使用服务编写自己的令牌模板。 创建后，使用 Azure 区块链令牌进行连接，并在区块链上颁发令牌。 发出后，可以在多个区块链网络中管理令牌。

## <a name="templates"></a>模板

使用 Azure 区块链令牌选择预生成的令牌模板或创建自己的令牌模板。 Azure 区块链令牌支持令牌模板可组合性，使你能够基于支持的行为创建自己的令牌模板。 令牌模板可用于大多数区块链解决方案，因为它们映射到最常使用的令牌。 可以从模板开始，对其进行个性化设置，并为解决方案部署令牌。

有关 Azure 区块链标记模板的详细信息，请参阅[Azure 区块链标记模板](templates.md)。

## <a name="management"></a>管理

Azure 区块链令牌提供 Azure 门户管理和 Api 来连接到现有的区块链网络。 目前，可以连接到[Azure 区块链服务](../service/overview.md)或其他以太坊系列区块链。

连接到一个或多个区块链网络后，你可以使用 Azure 区块链标记 Api 来颁发和管理要在区块链解决方案中使用的令牌。 使用 Api，你可以在你的业务应用程序和逻辑中集成令牌管理。 例如，可以使用 REST API 管理令牌，而不是直接在区块链上管理令牌。

## <a name="blockchains-and-accounts"></a>区块链和帐户

Azure 区块链令牌提供 Azure 门户管理和 Api，用于在连接的区块链网络上创建新组和新的区块链帐户。 你可以直接在已连接的网络上创建新帐户，而 Azure 区块链令牌会代表你管理你的帐户私钥。 使用组，可以将不同区块链帐户组合到多个网络中，并通过组来管理访问控制。

有关 Azure 区块链令牌帐户管理的详细信息，请参阅[Azure 区块链令牌帐户管理](account-management.md)。

## <a name="token-taxonomy-framework"></a>令牌分类框架

Azure 区块链令牌基于基于标准的基础，该基础名为令牌分类框架（.TTF）。 .TTF 是从[令牌分类计划](https://entethalliance.org/participate/token-taxonomy-initiative/)（TTI）令牌工作组创建的一组可交付结果。 TTI 工作组定义了可跨所有主要分类帐（包括以太坊、仲裁、Corda 和 Hyperledger Fabric）应用的令牌及其行为的业务分类。 工作组的目标是创建一个框架，用于标准化业务观点的令牌使用，以促进简化和变得大众化基于令牌的开发。 通过让行业在业务级别定义这些令牌及其行为，可以将令牌的详细实现从操作令牌的业务逻辑中分离出来。

## <a name="next-steps"></a>后续步骤

了解有关可用[Azure 区块链标记模板](templates.md)的详细信息。
