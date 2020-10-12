---
title: 新增功能 发行说明-Azure 区块链服务
description: 了解 Azure 区块链服务的新增功能，例如最新的发行说明、版本、已知问题和即将发生的更改。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 80ece6cb6bb81b7ce168da997603e17d1238171b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85921893"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure 区块链服务中的新增功能

> 通过复制并粘贴此 URL，获取有关何时通过复制并粘贴此 URL 来重新访问此页面的通知： `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` 到 rss 源读者[ ![ rss 源读者图标](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us)。

Azure 区块链服务不断地获得改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 新功能
- 版本升级
- 已知问题

---

## <a name="june-2020"></a>2020 年 6 月

### <a name="version-upgrades"></a>版本升级

- 仲裁版本升级到2.6.0。 利用版本2.6.0，可以发送签名的私有事务。 有关发送专用事务的详细信息，请参阅 [仲裁 API 文档](https://docs.goquorum.com/en/latest/Getting%20Started/api/)。
- Tessera 版本升级到0.10.5。

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>协定大小和事务大小增加到 128 KB

类型：配置更改

协定大小 (MaxCodeSize) 已增加到 128 KB，以便部署更大的智能协定。 此外， (txnSizeLimit) 的事务大小已增加到 128 KB。 配置更改适用于 19 2020 年6月之后在 Azure 区块链 Service 上创建的新 consortiums。

### <a name="trietimeout-value-reduced"></a>TrieTimeout 值减少

类型：配置更改

减小了 TrieTimeout 值，以便更频繁地将内存中状态写入磁盘。 值越低，在节点崩溃的罕见情况下就可以更快地恢复节点。

## <a name="may-2020"></a>2020 年 5 月

### <a name="version-upgrades"></a>版本升级

- Ubuntu 版本升级到18.04
- 仲裁版本升级到2.5。0
- Tessera 版本升级0.10。4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure 区块链服务支持发送 rawPrivate 事务

类型：功能

客户可以对节点上的帐户之外的私人交易进行签名。

### <a name="two-phase-member-provisioning"></a>两阶段成员预配

类型：增强

两个阶段有助于优化在长期现有的联合会中创建成员的方案。 在第一阶段预配成员基础结构。 在第二阶段中，成员与区块链同步。 两阶段预配有助于避免由于超时而导致成员创建失败。

## <a name="known-issues"></a>已知问题

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>estimateGas 函数在仲裁 v 2.6.0 中引发异常

在仲裁 v 2.6.0 中，对 *estimateGas* 函数的调用而不提供附加 *值* 参数会导致 *方法处理程序崩溃* 异常。 仲裁团队已收到通知，并且预计在7月2020日结束。 你可以使用以下解决方法，直到有可用的修补程序：

- 避免使用 *estimateGas* ，因为它会影响性能。 有关 estimateGas 性能问题的详细信息，请参阅 [调用 estimateGas 函数可降低性能](#calling-ethestimategas-function-reduces-performance)。 包括每个事务的汽油值。 如果未提供会导致仲裁 v 2.6.0 崩溃的汽油值，大多数库将调用 estimateGas。
- 如果需要调用 *estimateGas*，仲裁团队建议将附加参数 *值* 作为 *0* 作为解决方法传递。

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>如果验证程序节点少于四个，则挖掘停止

生产网络应该至少具有四个验证器节点。 仲裁建议至少需要四个验证器节点才能满足 IBFT 崩溃容错 (3F + 1) 。 应该至少有两个 Azure 区块链 Service *标准* 层节点，才能获取四个验证程序节点。 使用两个验证器节点预配标准节点。  

如果 Azure 区块链服务上的区块链网络没有四个验证器节点，则挖掘可能会在网络上停止。 可以通过对已处理的块设置警报来检测已停止挖掘。 在运行状况良好的网络中，每个节点每五分钟处理的块为60块。

作为一种缓解措施，Azure 区块链服务团队必须重新启动该节点。 客户需要打开支持请求才能重新启动该节点。 Azure 区块链服务团队正在努力自动检测和修复挖掘问题。

使用“标准”层进行生产级部署。 使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>区块链数据管理器需要标准层节点

如果使用区块链数据管理器，请使用 *标准* 层。 *基本*层仅有 4 GB 的内存。 因此，它无法扩展到区块链数据管理器以及它上运行的其他服务所需的使用量。

使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>大量解锁帐户调用导致 geth 崩溃

提交事务时，大量解锁帐户调用会导致 geth 在事务节点上崩溃。 因此，你必须停止引入事务。 否则，挂起的事务队列将增加。

Geth 会在不到一分钟的时间内自动重新启动。 同步可能需要较长时间，具体取决于节点。 Azure 区块链服务团队正在启用一项存档功能，该功能将减少同步的时间。

若要识别 geth 崩溃，可以在应用程序日志的区块链消息中检查日志中是否有任何错误消息。 您还可以检查处理的块在挂起事务增加时是否减少。

若要缓解此问题，请发送签名的事务，而不是使用命令来解锁帐户，而不是发送未签名的事务。 对于已进行外部签名的事务，无需解锁帐户。

如果要发送未签名的事务，请将0作为 "解除锁定" 命令中的时间参数发送，从而将该帐户解锁无限长时间。 提交所有事务后，可以将该帐户锁定回去。  

下面是 Azure 区块链服务使用的 geth 参数。 不能调整这些参数。

- 伊斯坦布尔块周期：5秒
- 地板气体限制：700000000
- Ceil 气体限制：800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>大量专用事务会降低性能

如果使用的是 Azure 区块链 Service 基本层和私有事务，则 Tessera 可能会崩溃。

可以通过查看区块链应用程序日志和搜索消息来检测 Tessera 崩溃 `Tessera crashed. Restarting Tessera...` 。

发生崩溃时，Azure 区块链服务会重启 Tessera。 重启大约一分钟。

如果要发送大量专用事务，请使用 *标准* 层。 使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="calling-ethestimategas-function-reduces-performance"></a>调用 estimateGas 函数可降低性能

多次调用 *estimateGas* 函数可减少每秒的事务数。 不要对每个提交的事务使用 *estimateGas* 函数。 *EstimateGas*函数消耗大量内存。

如果可能，请使用保守气体值提交事务，并将 *estimateGas*的使用降至最低。

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>智能协定中的无限循环可降低性能

避免在智能协定中出现无限循环，因为它们会降低性能。 有关更多信息，请参见以下资源：

- [避免未绑定循环](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [智能协定安全最佳做法](https://github.com/ConsenSys/smart-contract-best-practices)
- [仲裁提供的智能协定准则](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [适用于密度提供的汽油限制和循环的准则](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)
