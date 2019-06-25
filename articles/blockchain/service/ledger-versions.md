---
title: Azure 区块链服务支持分类帐版本、 修补和升级
description: 在 Azure 区块链服务，包括有关修补和系统管理系统和用户管理升级策略中的受支持的分类帐版本的概述。
services: azure-blockchain
keywords: 区块链
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399107"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支持的 Azure 区块链服务分类帐版本

Azure 区块链服务使用基于以太坊[仲裁](https://www.goquorum.com/developers)为已知参与方，标识为联盟 Azure 区块链服务中的组中的专用事务的处理而设计的分类帐。

目前，Azure 区块链服务支持[仲裁 2.2.1 版本](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1)并[Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

仲裁中的版本控制通过主要、 次要和修补程序版本。 例如，如果仲裁版本 2.0.1，发布类型将按以下方式分类：

|主版本 | Minor  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 第 | 

可从仲裁的 30 天内 azure 区块链服务到正在运行的现有成员自动更新仲裁的修补程序的版本。

## <a name="availability-of-new-ledger-versions"></a>新的分类帐版本

Azure 区块链服务的仲裁制造商提供可用的 60 天内提供的最新主版本号和次版本仲裁分类帐。 为财团预配新的成员和联盟时可以选择提供了最多四个次要版本。 目前不支持从升级为主要或次要版本。

## <a name="next-steps"></a>后续步骤

[在 Azure 区块链服务中的限制](limits.md)
