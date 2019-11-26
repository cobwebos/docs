---
title: Azure 区块链服务开发概述
description: 介绍如何在 Azure 区块链 Service 上开发解决方案。
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 3748a1ca473d817f536ba7c912d2485ffc14de2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455882"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 区块链服务开发概述

使用 Azure 区块链服务，可以创建联合会区块链网络，以实现企业方案，如资产跟踪、数字令牌、忠诚度和奖励、供应链金融和 provenance。 以下部分介绍了用于实现企业区块链解决方案的 Azure 区块链服务开发。

## <a name="connecting-to-azure-blockchain-service"></a>连接到 Azure 区块链服务

区块链网络有不同类型的客户端，包括完整节点、轻型节点和远程客户端。 Azure 区块链服务构建包含节点的区块链网络。 你可以使用不同的客户端作为 Azure 区块链服务的网关进行区块链开发。 Azure 区块链服务提供基本身份验证或访问密钥作为开发终结点。 下面是一些常用的客户端，可以使用 connect。

### <a name="visual-studio-code"></a>Visual Studio Code

可以使用 Azure 区块链开发工具包 Visual Studio Code 扩展连接到联合会成员。 连接到联盟后，可以编译、生成智能合同并将其部署到 Azure 区块链服务联盟成员。

有关详细信息，请参阅[快速入门：使用 Visual Studio Code 连接到 Azure 区块链 Service 联合会网络](connect-vscode.md)。

### <a name="metamask"></a>MetaMask

MetaMask 是一个基于浏览器的钱包（远程客户端）、RPC 客户端和基本协定资源管理器。 与其他浏览器 wallets 不同，MetaMask 将 web3 实例注入浏览器 JavaScript 上下文，作为连接到各种以太坊区块链（*mainnet*， *Ropsten testnet*， *Kovan testnet*，本地 RPC 节点，等等）。 可以轻松地将自定义 RPC 设置为连接到 Azure 区块链服务，并使用 Remix 开始区块链开发。

有关详细信息，请参阅[快速入门：使用 MetaMask 连接和部署智能协定](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是命令行接口，用于运行在中转中实现的完全以太坊节点。 无需运行完整节点，但可以启动其交互式控制台，它提供了一个公开 JavaScript API 的 JavaScript 运行时环境来与 Azure 区块链服务交互。

有关详细信息，请参阅[快速入门：使用 Geth 附加到 Azure 区块链 Service transaction 节点](connect-geth.md)。

## <a name="development-framework-configuration"></a>开发框架配置

若要开发复杂的企业区块链解决方案，需要使用开发框架连接到不同的区块链网络，并管理智能协定生命周期。

Truffle 是一种常用的区块链开发框架，用于在以太坊区块链上编写、编译、部署和测试分散的应用程序。 你还可以将 Truffle 看作是一个框架，它尝试无缝集成智能协定开发和传统 web 开发。

大多数项目与至少两个区块链节点进行交互。 开发人员在开发期间使用本地区块链。 当应用程序准备好进行测试或发布时，开发人员将部署到区块链网络。 例如，主公共以太坊网络或 Azure 区块链服务。 Truffle 可用于为每个网络编译和部署智能协定，并简化最终的应用程序部署。 有关详细信息，请参阅[快速入门：使用 Truffle 连接到 Azure 区块链服务网络](connect-truffle.md)。

## <a name="ethereum-quorum-private-transactions"></a>以太坊仲裁私有事务

仲裁是一个基于以太坊的分布式单协议，其中包含事务以及协定隐私和新的共识机制。 以太坊上的主要增强功能包括：

* **隐私**-仲裁通过公共和专用状态分离支持专用事务和私有协定，并利用对等加密消息交换，将专用数据定向到网络参与者。
* 授权网络不需要**替代的共识机制**，也不需要对工作或证据进行共识。 仲裁提供多种共识机制，适用于筏和 IBFT 等联盟链。  Azure 区块链服务使用 IBFT 共识机制。
* **对等**互连-使用智能协定的节点和对等节点可以确保只有已知的参与方可以加入网络。
* **更高的性能**-仲裁比公共 Geth 提供更高的性能。

## <a name="block-explorers"></a>阻止资源管理器

阻止资源管理器是联机区块链浏览器，用于显示单个块内容、事务地址数据和历史记录。 可以通过 Azure 区块链服务中的 Azure Monitor 获取基本的块信息。 但是，如果在开发过程中需要更多详细信息，则块资源管理器会很有用。  以下块资源管理器使用 Azure 区块链服务：

* 从 Web3 实验室[Epirus Azure 区块链 Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

还可以通过使用区块链数据管理器和 Azure Cosmos DB 来生成自己的块资源管理器，请参阅[教程：使用区块链数据管理器将数据发送到 Azure Cosmos DB](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 度量

由于区块链用于更多企业方案，因此，每秒事务数（TPS）速度非常重要，可避免瓶颈和系统效率低下。 在分散区块链中，事务速率较高可能很难维护。 精确的 TPS 度量可能受不同因素的影响，如服务器线程、事务队列大小、网络延迟和安全性。 如果在开发过程中需要衡量 TPS 速度，则会[ChainHammer](https://github.com/drandreaskrueger/chainhammer)一个流行的开源工具。

## <a name="next-steps"></a>后续步骤

尝试使用适用于以太坊的 Azure 区块链开发工具包附加到 Azure 区块链服务上的联合会。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)