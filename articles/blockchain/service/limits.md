---
title: Azure 区块链服务限制
description: Azure 区块链服务中的服务和功能限制概述
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529574"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 区块链服务中的限制

Azure 区块链服务具有服务和功能限制，例如成员可以拥有的节点数、联合体限制和存储量。

## <a name="pricing-tier"></a>定价层

事务和验证器节点的最大限制取决于是在基本定价层还是标准定价层提供 Azure 区块链服务。

| 定价层 | 最大事务节点 | 最大验证器节点 |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

您的联合体网络应至少具有两个 Azure 区块链服务标准层节点。 标准层节点包括两个验证器节点。 满足[伊斯坦布尔拜占庭容错共识](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)需要四个验证节点。

使用基本层用于概念的开发、测试和验证。 对生产级部署使用标准层。 如果您使用的是区块链数据管理器或发送大量私人交易，则还应使用*标准*层。

不支持在成员创建后更改基本和标准之间的定价层。

## <a name="storage-capacity"></a>存储容量

每个节点可用于分类帐数据和日志的最大存储量为 1.8 TB。

不支持减小分类帐和日志存储大小。

## <a name="consortium-limits"></a>联盟限制

* **联盟和成员名称必须与**Azure 区块链服务中的其他联合体和成员名称是唯一的。

* **不能更改成员和联合体名称**

* **联合体中的所有成员必须位于同一定价层中**

* **参加联合体的所有成员必须居住在同一区域**

    在联合体中创建的第一个成员指示该区域。 受邀加入联合体的成员必须居住在第一个成员所在的同一区域。 将所有成员限制在同一区域有助于确保网络共识不会受到负面影响。

* **联合体必须至少有一个管理员**

    如果联合体中只有一个管理员，则在联合体中添加或提升另一个管理员之前，他们无法将自己从联合体中删除或删除其成员。

* **无法再次添加从联合体中删除的成员**

    相反，必须重新邀请他们加入联合体，并创建一个新成员。 不会删除其现有成员资源以保留历史事务。

* **联合体中的所有成员必须使用相同的分类帐版本**

    有关 Azure 区块链服务中提供的修补、更新和分类帐版本的详细信息，请参阅[修补、更新和版本](ledger-versions.md)。

## <a name="next-steps"></a>后续步骤

了解有关系统修补和升级的策略的详细信息 -[修补、更新和版本](ledger-versions.md)。
