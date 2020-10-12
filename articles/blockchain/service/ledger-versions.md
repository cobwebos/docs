---
title: Azure 区块链服务分类帐版本、修补 & 升级
description: Azure 区块链服务中支持的分类帐版本的概述。 包括用于修补和升级系统的策略。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85807734"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支持的 Azure 区块链服务分类帐版本

Azure 区块链服务使用基于以太坊的 [仲裁](https://www.goquorum.com/developers) 分类帐在一组已知的参与者（在 Azure 区块链服务中标识为协会）中处理专用交易。

目前，Azure 区块链服务支持 [仲裁版本 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) 和 [Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

仲裁中的版本控制通过主要、次要和修补程序版本完成。 例如，如果仲裁版本为2.0.1，则版本类型将按如下方式分类：

|主要 | 次要  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 区块链服务会自动将仲裁的修补程序版本更新为30天内的现有运行成员，使其可从仲裁获得。

## <a name="availability-of-new-ledger-versions"></a>新分类帐版本的可用性

在从仲裁制造商处获取的60天内，Azure 区块链服务提供仲裁分类帐的最新主要和次要版本。 在预配新成员和协会时，最多可为 consortia 提供四个次要版本。 当前不支持从升级到主要或次要版本。 例如，如果运行的是版本2.x，则当前不支持升级到版本1.x。 同样，如果你运行的是版本2.2，则当前不支持升级到版本2.3。

## <a name="how-to-check-quorum-ledger-version"></a>如何检查仲裁分类帐版本

可以通过使用 geth 附加到节点或查看诊断日志来检查 Azure 区块链服务成员的仲裁版本。

### <a name="using-geth"></a>使用 geth

使用 geth 附加到 Azure 区块链服务节点。 例如，`geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`。

节点连接后，geth 将报告类似于以下输出的仲裁版本：

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

有关使用 geth 的详细信息，请参阅 [快速入门：使用 geth 附加到 Azure 区块链 Service transaction 节点](connect-geth.md)。

### <a name="using-diagnostic-logs"></a>使用诊断日志

如果启用诊断日志，则会报告事务节点的仲裁版本。 例如，以下节点信息日志消息包括仲裁版本。

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

有关诊断日志的详细信息，请参阅 [通过 Azure Monitor 监视 Azure 区块链服务](monitor-azure-blockchain-service.md#diagnostic-settings)。

## <a name="how-to-check-genesis-file-content"></a>如何检查 genesis 文件内容

若要检查区块链节点的 genesis 文件内容，可以使用以下以太坊 JavaScript API：

``` bash
admin.nodeInfo.protocols
```
可以使用 geth 控制台或 web3 库调用 API。 有关使用 geth 的详细信息，请参阅 [快速入门：使用 geth 附加到 Azure 区块链 Service transaction 节点](connect-geth.md)。

## <a name="next-steps"></a>后续步骤

[Azure 区块链服务中的限制](limits.md)
