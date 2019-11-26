---
title: Azure 区块链服务分类帐版本、修补 & 升级
description: Azure 区块链服务中支持的分类帐版本的概述，包括有关系统修补和系统管理的和用户管理的升级的策略。
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325189"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支持的 Azure 区块链服务分类帐版本

Azure 区块链服务使用基于以太坊的[仲裁](https://www.goquorum.com/developers)分类帐在一组已知的参与者（在 Azure 区块链服务中标识为协会）中处理专用交易。

目前，Azure 区块链服务支持[仲裁版本 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3)和[Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

仲裁中的版本控制通过主要、次要和修补程序版本完成。 例如，如果仲裁版本为2.0.1，则版本类型将按如下方式分类：

|主版本 | Minor  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 区块链服务会自动将仲裁的修补程序版本更新为30天内的现有运行成员，使其可从仲裁获得。

## <a name="availability-of-new-ledger-versions"></a>新分类帐版本的可用性

在从仲裁制造商处获取的60天内，Azure 区块链服务提供仲裁分类帐的最新主要和次要版本。 在预配新成员和协会时，最多可为 consortia 提供四个次要版本。 当前不支持从升级到主要或次要版本。 例如，如果运行的是版本2.x，则当前不支持升级到版本1.x。 同样，如果你运行的是版本2.2，则当前不支持升级到版本2.3。

## <a name="next-steps"></a>后续步骤

[Azure 区块链服务中的限制](limits.md)
