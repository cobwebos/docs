---
title: Azure 区块链服务开发概述
description: 介绍如何在 Azure 区块链 Service 上开发解决方案。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 6f27dd199cc054d128a4f46b222c7207d5975efb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241009"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 区块链服务开发概述

使用 Azure 区块链服务，可以创建联合会区块链网络，以实现企业方案，如资产跟踪、数字令牌、忠诚度和奖励、供应链金融和 provenance。 本文介绍了 Azure 区块链服务开发和实现企业区块链的关键主题。

## <a name="client-connection-to-azure-blockchain-service"></a>与 Azure 区块链服务的客户端连接

区块链网络有不同类型的客户端，包括完整节点、轻型节点和远程客户端。 Azure 区块链服务构建包含节点的区块链网络。 你可以使用不同的客户端作为 Azure 区块链服务的网关进行区块链开发。 Azure 区块链服务提供基本身份验证或访问密钥作为开发终结点。 下面是一些常用的客户端，可以使用 connect。

### <a name="metamask"></a>MetaMask

MetaMask 是一个基于浏览器的钱包（远程客户端）、RPC 客户端和基本协定资源管理器。 与其他浏览器 wallets 不同，MetaMask 将 web3 实例注入浏览器 JavaScript 上下文，作为连接到各种以太坊区块链（*mainnet*， *Ropsten testnet*， *Kovan testnet*，本地 RPC 节点，等等）。 可以轻松地将自定义 RPC 设置为连接到 Azure 区块链服务，并使用 Remix 开始区块链开发。

### <a name="geth"></a>Geth

Geth 是命令行接口，用于运行在中转中实现的完全以太坊节点。 无需运行完整节点，但可以启动其交互式控制台，它提供了一个公开 JavaScript API 的 JavaScript 运行时环境来与 Azure 区块链服务交互。

## <a name="development-framework-configuration"></a>开发框架配置

若要开发复杂的企业区块链解决方案，需要使用开发框架连接到不同的区块链网络，管理智能协定生命周期，自动进行测试，使用脚本部署智能协定，并提供交互式控制台。

Truffle 是一种常用的区块链开发框架，用于在以太坊区块链上编写、编译、部署和测试分散的应用程序。 你还可以将 Truffle 看作是一个框架，它尝试无缝集成智能协定开发和传统 web 开发。

即使是最小的项目与至少两个区块链节点交互：一个在开发人员的计算机上，另一个表示开发人员在其中部署应用程序的网络。 例如，主公共以太坊网络或 Azure 区块链服务。 Truffle 提供一个系统，用于管理每个网络的编译和部署项目，并以简化最终应用程序部署的方式实现此目的。 有关详细信息，请参阅[快速入门：使用 Truffle 连接到 Azure 区块链服务网络](connect-truffle.md)。

## <a name="ethereum-quorum-private-transaction"></a>以太坊仲裁私有事务

仲裁是一个基于以太坊的分布式单协议，其中包含事务以及协定隐私和新的共识机制。 以太坊上的主要增强功能包括：

* 隐私-仲裁通过公共和专用状态分离支持专用事务和私有协定，并利用对等加密消息交换，将专用数据定向到网络参与者。
* 替代的共识机制-无需在授权网络中进行工作证明或有证据验证的共识。 仲裁提供多种共识机制，适用于筏和 IBFT 等联盟链。  Azure 区块链服务使用 IBFT 共识机制。

* 对等互连-使用智能协定的节点和对等节点，确保只有已知的参与方可以加入网络
* 更高的性能-仲裁比公共 Geth 提供更高的性能

有关分步说明，请参阅[教程：使用 Azure 区块链服务](send-transaction.md)发送事务，以获取专用事务的示例。

## <a name="block-explorers"></a>阻止资源管理器

阻止资源管理器是联机区块链浏览器，用于显示单个块内容、事务地址数据和历史记录。 可以通过 Azure 区块链服务中的 Azure Monitor 获取基本块信息，但是，如果在开发过程中需要更多详细信息，则块资源管理器会很有用。  你可以使用常用的开源块资源管理器。 下面是适用于 Azure 区块链服务的块浏览器列表：

* Web3 实验室中的[Azure 区块链服务资源管理器](https://web3labs.com/azure-offer)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS 度量

由于区块链用于更多企业方案，因此，每秒事务数（TPS）速度非常重要，可避免瓶颈和系统效率低下。 在分散区块链中，事务速率较高可能很难维护。 精确的 TPS 度量可能受不同因素的影响，如服务器线程、事务队列大小、网络延迟和安全性。 如果在开发过程中需要衡量 TPS 速度，则会[ChainHammer](https://github.com/drandreaskrueger/chainhammer)一个流行的开源工具。

## <a name="next-steps"></a>后续步骤

[快速入门：使用 Truffle 连接到 Azure 区块链服务网络](connect-truffle.md)
