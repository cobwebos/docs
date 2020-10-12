---
title: DoD 影响级别 5 蓝图示例概述
description: DoD 影响级别 5 示例的概述。 此蓝图示例可帮助客户评估特定 DoD 影响级别 5 控制要求。
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: 15e2c5990ba32363441f8ca7cd19672cadc16d51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90978088"
---
# <a name="overview-of-the-dod-impact-level-5-blueprint-sample"></a>DoD 影响级别 5 蓝图示例概述

国防部影响级别 5 (DoD IL5) 蓝图示例提供了监管防护措施，其中使用 [Azure Policy](../../../policy/overview.md) 来帮助评估特定 DoD 影响级别 5 控制要求。 此蓝图可帮助客户为 Azure 部署的任何必须实现 DoD 影响级别 5 控制要求的体系结构部署一组核心策略。 有关哪些 Azure 云和服务符合 DoD 影响级别 5 授权要求的最新信息，请参阅[按 FedRAMP 和 DoD CC SRG 审核范围列出的 Azure 服务](../../../../azure-government/compliance/azure-services-in-fedramp-auditscope.md)。

> [!NOTE]
> 该蓝图示例在 Azure 政府中提供。

## <a name="control-mapping"></a>控制映射

控制映射部分提供了有关包含在此蓝图内的策略的详细信息，以及这些策略如何满足 DoD 影响级别 5 中的各种控制要求。 分配给一个体系结构时，资源由 Azure Policy 评估是否不符合已分配的策略。 有关详细信息，请参阅 [Azure Policy](../../../policy/overview.md)。

## <a name="next-steps"></a>后续步骤

你已查看了 DoD 影响级别 5 蓝图示例概述。 接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [DoD 影响级别 5 蓝图 - 控制映射](./control-mapping.md)
> [DoD 影响级别 5 蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。