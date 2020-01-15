---
title: Azure Cosmos 模拟器下载和发行说明
description: 获取不同版本的 Azure Cosmos 模拟器发行说明并下载信息。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: e14ee617aca20fc18226651acfadab6ab2bdd939
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445297"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos 模拟器 - 发行说明和下载信息

本文显示了 Azure Cosmos 模拟器发行说明，其中包含每个发行版中所做的功能更新列表。 它还列出了要下载和使用的模拟器的最新版本。

## <a name="download"></a>下载

| | |
|---------|---------|
|**MSI 下载**|[Microsoft 下载中心](https://aka.ms/cosmosdb-emulator)|
|**入门**|[使用 Azure Cosmos 模拟器在本地开发](local-emulator.md)|

## <a name="release-notes"></a>发行说明

### <a name="272"></a>2.7.2

- 此版本向 Cosmos 模拟器添加了 MongoDB 版本 3.6 服务器支持。 若要启动针对服务 3.6 版本的 MongoDB 终结点，请使用“/EnableMongoDBEndpoint=3.6”选项从管理员命令行启动模拟器。

### <a name="270"></a>2.7.0

- 此版本修复了一个回归，该回归阻止用户在使用基于 .Net Core 或 x86 .NET 的客户端时从模拟器对 SQL API 帐户执行查询。

### <a name="246"></a>2.4.6

- 除了[使用 Azure Cosmos 模拟器在本地开发](local-emulator.md)中所述的例外情况，此版本提供了与到 2019 年 7 月为止的 Azure Cosmos 服务中的功能同等的功能。 它还修复了与通过命令行调用时模拟器关闭相关的几个 bug，以及使用直接模式连接的 SDK 客户端的内部 IP 地址覆盖。

### <a name="243"></a>2.4.3

- 默认情况下已禁止启动 MongoDB 服务。 默认情况下仅启用 SQL 终结点。 用户必须使用模拟器的“/EnableMongoDbEndpoint”命令行选项手动启动终结点。 现在，它就像所有其他服务终结点（例如 Gremlin、Cassandra 和表）一样。
- 修复了使用“/AllowNetworkAccess”启动时模拟器中的 bug，即 Gremlin、Cassandra 和表终结点无法正确处理外部客户端发出的请求。
- 将直接连接端口添加到“防火墙规则”设置。

### <a name="240"></a>2.4.0

- 修复了当主计算机上存在网络监视应用（如 Pulse Client）时模拟器无法启动的问题。
