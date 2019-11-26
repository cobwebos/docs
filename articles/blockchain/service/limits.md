---
title: Azure 区块链服务限制
description: Azure 区块链服务中的服务和功能限制概述
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455659"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 区块链服务中的限制

Azure 区块链服务具有服务和功能限制，如成员可以具有的节点数、联合会限制和存储量。

## <a name="pricing-tier"></a>定价层

事务和验证器节点的最大限制取决于是否在基本或标准定价层预配 Azure 区块链服务。

| 定价层 | 最大事务节点数 | 最大验证器节点 |
|:---|:---:|:---:|
| 基本 | 10 | 1 |
| 标准 | 10 | 2 |

不支持在创建成员后更改基本和标准定价层。

## <a name="storage-capacity"></a>存储容量

每个节点用于分类帐数据和日志的最大存储量为 1.8 tb。

不支持降低分类帐和日志存储大小。

## <a name="consortium-limits"></a>协会限制

* **联盟和成员名称必须**与 Azure 区块链服务中的其他联盟和成员名称唯一。

* **不能更改成员和联盟名称**

* **联盟中的所有成员都必须在同一定价层中**

* **参与联盟的所有成员必须位于同一区域**

    在联盟中创建的第一个成员决定区域。 联盟的受邀成员必须与第一个成员位于同一区域。 将所有成员限制为同一区域有助于确保网络共识不会受到负面影响。

* **联盟至少必须有一个管理员**

    如果联盟中只有一个管理员，则他们不能将其自身从协会中删除，也不能删除其成员，直到在协会中添加或升级其他管理员。

* **不能再次添加从联合会删除的成员**

    相反，他们必须重新邀请加入联盟并创建新成员。 不会删除其现有成员资源来保留历史事务。

* **联盟中的所有成员都必须使用同一分类帐版本**

    有关 Azure 区块链服务中可用的修补、更新和分类帐版本的详细信息，请参阅[修补程序、更新和版本](ledger-versions.md)。

## <a name="next-steps"></a>后续步骤

详细了解有关系统修补和升级的策略-[修补、更新和版本](ledger-versions.md)。
