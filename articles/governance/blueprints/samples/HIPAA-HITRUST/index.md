---
title: HIPAA HITRUST 蓝图示例概述
description: HIPAA HITRUST 蓝图示例概述。 此蓝图示例可帮助客户评估特定的 HIPAA HITRUST 控制。
ms.date: 12/3/2019
ms.topic: sample
ms.openlocfilehash: ebe907053b37ebe0ff99248aa181a480c045d533
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470060"
---
# <a name="overview-of-the-hipaa-hitrust-blueprint-sample"></a>HIPAA HITRUST 蓝图示例概述

HIPAA HITRUST 蓝图示例提供了一组监管防护措施，这些措施使用 [Azure Policy](../../../policy/overview.md) 来帮助用户通过 [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) 认证。 

## <a name="blueprint-sample"></a>蓝图示例

如果 Azure 部署的任一体系结构需要符合 HITRUST HIPAA 框架或通过其认证，此蓝图示例可帮助客户为该体系结构部署一组核心策略。 [控件映射](./control-mapping.md)部分提供了包含在此计划内的策略的相关详细信息，以及如何通过这些策略来满足 HITRUST HIPAA 框架定义的各种控制的要求。 分配给一个体系结构时，资源由 Azure Policy 评估是否符合已分配的策略。

## <a name="next-steps"></a>后续步骤

你已查看了 HIPAA HITRUST 蓝图示例概述。 接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [HIPAA HITRUST 蓝图 - 控制映射](./control-mapping.md)
> [HIPAA HITRUST 蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。