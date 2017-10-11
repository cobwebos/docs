---
title: "适用于 Azure Cosmos DB 的 Azure PowerShell 示例 | Microsoft Docs"
description: "Azure PowerShell 示例 - 可用于创建和管理 Azure Cosmos DB 帐户的脚本。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 7698e03c0dc8d1c6d1e926f45e903a909bfd0c93
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>适用于 Azure Cosmos DB 的 Azure PowerShell 示例

下表包含指向适用于 Azure Cosmos DB 的示例 Azure PowerShell 脚本的链接。

| |  |
|---|---|
|**创建 Azure Cosmos DB 帐户**||
|[创建 DocumentDB API 帐户](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建单个要用于 DocumentDB API 的 Azure Cosmos DB 帐户。 |
|**缩放 Azure Cosmos DB**||
|[将 Azure Cosmos DB 帐户复制到多个区域中并配置故障转移优先级](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|在全球范围内将帐户数据复制到具有指定故障转移优先级的多个区域中。|
|**保护 Azure Cosmos DB**||
| [获取帐户密钥](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 获取帐户的主要和辅助 master 写密钥以及主要和辅助只读密钥。|
| [获取 MongoDB 连接字符串](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 获取用于将 MongoDB 应用连接到 Azure Cosmos DB 帐户的连接字符串。|
|[重新生成帐户密钥](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|重新生成帐户的 master 密钥或只读密钥。|
|[创建防火墙](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建入站 IP 访问控制策略，仅允许从获批准的一组计算机和/或云服务访问帐户。|
|**高可用性、灾难恢复、备份和还原**||
|[配置故障转移策略](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|为帐户所复制的每个区域设置故障转移优先级。|
|||