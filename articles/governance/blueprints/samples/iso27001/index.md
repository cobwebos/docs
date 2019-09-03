---
title: 示例 - ISO 27001 蓝图 - 概述
description: ISO 27001 蓝图示例概述。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 59db15439f2391796f54cc8edd5dbf8e554e6379
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232736"
---
# <a name="overview-of-the-iso-27001-blueprint-sample"></a>ISO 27001 蓝图示例概述

ISO 27001 蓝图示例提供了监管防护措施，其中使用 [Azure Policy](../../../policy/overview.md) 来帮助评估特定 ISO 27001 控制要求。 对于 Azure 部署的任何必须实现 ISO 27001 控制要求的体系结构，此蓝图可帮助客户为其部署一组核心策略。 此外，还提供了另外两个 ISO 27001 蓝图示例，它们可帮助你部署[基础体系结构](../iso27001-shared/index.md)和 [ASE/SQL 工作负荷](../iso27001-ase-sql-workload/index.md)。

## <a name="control-mapping"></a>控制映射

控制映射部分提供了有关包含在此蓝图内的策略的详细信息，以及这些策略如何满足 ISO 27001 中的各种控制要求。 分配给一个体系结构时，资源由 Azure Policy 评估是否不符合已分配的策略。 有关详细信息，请参阅 [Azure Policy](../../../policy/overview.md)。

## <a name="next-steps"></a>后续步骤

你已查看了 ISO 27001 蓝图示例的概述和体系结构。
接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [ISO 27001 蓝图 - 控制映射](./control-mapping.md)
> [ISO 27001 蓝图 - 部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
