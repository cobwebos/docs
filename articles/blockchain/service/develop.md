---
title: Azure 区块链服务开发概述
description: 有关开发 Azure 区块链服务解决方案的介绍。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348378"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 区块链服务开发概述

借助 Azure 区块链服务，您可以创建联合区块链网络，以启用企业方案，如资产跟踪、数字令牌、忠诚度和奖励、供应链财务和来源。 以下各节介绍用于实现企业区块链解决方案的 Azure 区块链服务开发。

## <a name="connecting-to-azure-blockchain-service"></a>连接到 Azure 区块链服务

区块链网络有不同类型的客户端，包括完整节点、光节点和远程客户端。 Azure 区块链服务构建包含节点的区块链网络。 您可以使用不同的客户端作为 Azure 区块链服务的网关，用于区块链开发。 Azure 区块链服务作为开发终结点提供基本身份验证或访问密钥。 以下是可以使用连接的热门客户端。

### <a name="visual-studio-code"></a>Visual Studio Code

您可以使用 Azure 区块链开发工具包可视化工作室代码扩展连接到联合成员。 连接到联盟后，可以编译、生成智能合同并将其部署到 Azure 区块链服务联盟成员。

为了开发复杂的企业区块链解决方案，需要一个开发框架来连接到不同的区块链网络并管理智能合同生命周期。 大多数项目至少与两个区块链节点交互。 开发人员在开发过程中使用本地区块链。 当应用程序准备好进行测试或发布时，开发人员将部署到区块链网络。 例如，主要公共以地庭网络或 Azure 区块链服务。 Visual Studio 代码中用于以地庭扩展的 Azure 区块链开发工具包使用 Truffle。 Truffle 是一个流行的区块链开发框架，用于在以泰伦区块链上编写、编译、部署和测试分散的应用程序。 您还可以将 Truffle 视为一个框架，试图无缝集成智能合约开发和传统 Web 开发。

有关详细信息，请参阅[快速入门：使用可视化工作室代码连接到 Azure 区块链服务联合体网络](connect-vscode.md)。

### <a name="metamask"></a>元框

MetaMask 是基于浏览器的钱包（远程客户端）、RPC 客户端和基本合同资源管理器。 与其他浏览器钱包不同，MetaMask 将 Web3 实例注入浏览器 JavaScript 上下文，充当 RPC 客户端，连接到各种以太坊区块链（*主网**、Ropsten 测试网*、*科万测试网*、本地 RPC 节点等）。 您可以轻松设置自定义 RPC 以连接到 Azure 区块链服务，并使用混音启动区块链开发。

有关详细信息，请参阅[快速入门：使用 MetaMask 连接和部署智能合约](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是运行在 Go 中实现的完整以非庭节点的命令行接口。 您无需运行完整节点，但可以启动其交互式控制台，该控制台提供一个 JavaScript 运行时环境，公开 JavaScript API 与 Azure 区块链服务进行交互。

有关详细信息，请参阅[快速入门：使用 Geth 附加到 Azure 区块链服务事务节点](connect-geth.md)。

## <a name="ethereum-quorum-private-transactions"></a>以天特鲁姆私人交易

仲裁是一种基于以里瑟姆的分布式分类账协议，具有事务加上合同隐私和新的共识机制。 Go-以地庭的主要增强功能包括：

* **隐私**- Quorum 通过公共和私有状态分离支持私人交易和私人合同，并利用点对点加密消息交换将私人数据传输给网络参与者。
* **其他共识机制**- 工作证明或风险证明共识不需要许可网络。 Quorum 为 RAFT 和 IBFT 等财团链提供了多种共识机制。Azure 区块链服务使用 IBFT 共识机制。
* 使用智能合约**的对等权限**节点和对等权限可确保只有已知方才能加入网络.
* **更高的性能**- Quorum 提供比公共 Geth 更高的性能。

## <a name="block-explorers"></a>阻止资源管理器

块资源管理器是在线区块链浏览器，显示单个块内容、事务地址数据和历史记录。 基本块信息可通过 Azure 区块链服务中的 Azure 监视器获得。 但是，如果您在开发过程中需要更详细的信息，则块资源管理器可能很有用。  以下块资源管理器与 Azure 区块链服务配合使用：

* 来自 Web3 实验室的[Epirus Azure 区块链服务资源管理器](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [块Scout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

您还可以使用区块链数据管理器和 Azure Cosmos DB 构建自己的块资源管理器，请参阅[教程：使用区块链数据管理器将数据发送到 Azure Cosmos DB](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 测量

由于区块链用于更多的企业方案，每秒交易 （TPS） 速度对于避免瓶颈和系统效率低下非常重要。 高事务速率可能很难在分散的区块链中维护。 准确的 TPS 测量可能受不同因素的影响，例如服务器线程、事务队列大小、网络延迟和安全性。 如果您需要在开发过程中测量 TPS 速度，一个流行的开源工具是[链哈默](https://github.com/drandreaskrueger/chainhammer)。

## <a name="next-steps"></a>后续步骤

尝试使用以地坊的 Azure 区块链开发工具包快速入门，以附加到 Azure 区块链服务上的联合体。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)