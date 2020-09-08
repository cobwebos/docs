---
title: Azure 认知搜索的 Azure Policy 法规遵从性控制
description: 列出可用于 Azure 认知搜索的 Azure Policy 法规遵从性控制。 这些内置的策略定义提供了管理 Azure 资源符合性的常用方法。
ms.date: 09/04/2020
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 8f35c09d0d27136b0244055f074f22c260f323e4
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482194"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure 认知搜索的 Azure Policy 法规遵从性控制

如果你使用 [Azure Policy](../governance/policy/overview.md) 强制实施 [Azure 安全基准](../security/benchmarks/introduction.md)中的建议，则可能已知道可以创建用于标识和修复不符合服务的策略。 这些策略可以是自定义的，也可以基于内置定义，这些定义针对易于理解的最佳做法提供符合性条件和合适的解决方案。

对于 Azure 认知搜索，当前有一个可以在策略分配中使用的生成定义（在下面列出）。 该内置定义用于日志记录和监视。 通过在[创建的策略](../governance/policy/assign-policy-portal.md)中使用此内置定义，系统将扫描没有[诊断日志记录](search-monitor-logs.md)的搜索服务，然后相应地进行启用。

[Azure Policy 中的法规符合性](../governance/policy/concepts/regulatory-compliance.md)为与不同符合性标准相关的“符合域”和“安全控件”提供 Microsoft 创建和管理的计划定义，称为“内置”。 此页列出 Azure 认知搜索的“符合域”和“安全控件” 。 可以分别为“安全控制”分配内置项，以帮助 Azure 资源符合特定的标准。

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Policy 法规符合性](../governance/policy/concepts/regulatory-compliance.md)。
- 在 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy)中查看这些内置项。
