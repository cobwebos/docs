---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/08/2020
ms.author: larryfr
ms.openlocfilehash: c43b8b211b827a38db75bed17c23e684e54922a5
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996344"
---
> [!IMPORTANT]
> 若要避免在没有作业运行时产生费用，请**将最小节点数设置为 0**。 此设置允许 Azure 机器学习在未使用节点时取消分配这些节点。 任何大于0的值都将使该数量的节点保持运行状态，即使它们未被使用也是如此。