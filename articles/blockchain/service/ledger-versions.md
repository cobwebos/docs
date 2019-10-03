---
title: Azure 区块链 Service 支持的分类帐版本、修补和升级
description: Azure 区块链服务中支持的分类帐版本的概述, 包括有关系统修补和系统管理的和用户管理的升级的策略。
services: azure-blockchain
keywords: 区块链
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 6cded49f14ae90f84b18c0a37059677c6c1fed99
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562787"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支持的 Azure 区块链服务分类帐版本

Azure 区块链服务使用基于以太坊的[仲裁](https://www.goquorum.com/developers)分类帐在一组已知的参与者 (在 Azure 区块链服务中标识为协会) 中处理专用交易。

目前, Azure 区块链服务支持[仲裁版本 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3)和[Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

仲裁中的版本控制通过主要、次要和修补程序版本完成。 例如, 如果仲裁版本为 2.0.1, 则版本类型将按如下方式分类:

|主版本 | 次版本  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 区块链服务会自动将仲裁的修补程序版本更新为30天内的现有运行成员, 使其可从仲裁获得。

## <a name="availability-of-new-ledger-versions"></a>新分类帐版本的可用性

在从仲裁制造商处获取的60天内, Azure 区块链服务提供仲裁分类帐的最新主要和次要版本。 在预配新成员和协会时, 最多可为 consortia 提供四个次要版本。 当前不支持从升级到主要或次要版本。

## <a name="next-steps"></a>后续步骤

[Azure 区块链服务中的限制](limits.md)
