---
title: 介质蓝图示例概述
description: 介质蓝图示例概述。 此蓝图示例可帮助客户评估特定介质控制要求。
ms.date: 02/26/2020
ms.topic: sample
ms.openlocfilehash: ee4f15d52eea668ebffb8d5f639dd9c86ae04610
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201500"
---
# <a name="overview-of-the-media-blueprint-sample"></a>介质蓝图示例概述

介质蓝图示例提供了一组监管防护措施，这些措施使用 [Azure Policy](../../../policy/overview.md) 来帮助用户通过[介质](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)认证。 

## <a name="blueprint-sample"></a>蓝图示例

如果 Azure 部署的任一体系结构需要符合介质框架或通过其认证，此蓝图示例可帮助客户为该体系结构部署一组核心策略。 [控制映射](./control-mapping.md)部分提供了有关包含在此计划内的策略的详细信息，以及这些策略如何帮助满足由介质框架定义的各种控制要求。 分配给一个体系结构时，资源由 Azure Policy 评估是否符合已分配的策略。

## <a name="next-steps"></a>后续步骤

你已查看了介质蓝图示例概述。 接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [介质蓝图 - 控制映射](./control-mapping.md)
> [介质蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。