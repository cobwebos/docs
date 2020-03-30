---
title: Azure 区块链服务分类帐版本、修补、&升级
description: Azure 区块链服务中受支持的分类帐版本的概述，包括有关系统修补、系统管理和用户管理升级的策略。
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325189"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>受支持的 Azure 区块链服务分类帐版本

Azure 区块链服务使用基于以非坊[的 Quorum](https://www.goquorum.com/developers)分类账，用于处理一组已知参与者中的私人事务，这些参与者在 Azure 区块链服务中标识为联合体。

目前，Azure 区块链服务支持[Quorum 版本 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3)和[Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

在"仲裁"中版本控制是通过主要、次要和修补程序版本完成的。 例如，如果 Quorum 版本为 2.0.1，则发布类型将分类如下：

|主要 | Minor  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 区块链服务在从 Quorum 提供后 30 天内自动将 Quorum 的修补程序版本更新到现有正在运行的成员。

## <a name="availability-of-new-ledger-versions"></a>新分类帐版本的可用性

Azure 区块链服务在从仲裁制造商获得后 60 天内提供最新的主要和次要版本的 Quorum 分类帐。 在预配新成员和联合体时，最多提供四个次要版本供联营集团选择。 当前不支持从主要或次要版本升级到。 例如，如果您正在运行版本 2.x，则当前不支持升级到版本 3.x。 同样，如果您正在运行版本 2.2，则当前不支持升级到版本 2.3。

## <a name="next-steps"></a>后续步骤

[Azure 区块链服务中的限制](limits.md)
