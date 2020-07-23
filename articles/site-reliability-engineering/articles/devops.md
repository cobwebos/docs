---
title: 常见问题解答： SRE 和 DevOps |Microsoft Docs
titleSuffix: Azure
description: 常见问题解答：了解 SRE 与 DevOps 之间的关系
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 04/22/2020
ms.author: dnb
ms.openlocfilehash: e917c609b484b1a58377fea2f6cdd75dde30ca6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82196408"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>常见问题： SRE 与 DevOps 之间的关系是什么？

站点可靠性工程与 DevOps 之间的关系有一系列常见问题，其中包括 "它们之间的关系如何？ 那么其不同之处在哪里？ 我们是否可以同时在我们的组织中使用？ "。 本文试图共享由 SRE 和 DevOps 社区提供的一些答复，这些答复更接近了解此关系。

## <a name="how-are-they-the-same"></a>它们有何不同？

SRE 和 DevOps 都是新式操作实践，它们是为应对包括的挑战而创建和开发的：

- 生产环境和开发过程日益复杂
- 提高对这些环境的连续运行的业务依赖关系
- 无法以这些环境的大小线性缩放工作人员
- 在仍保持运营稳定性的同时，需要更快地移动

这两个操作做法的价值是对处理这些挑战（如监视/可观察性、自动化、文档和协作软件开发工具）至关重要的对象。

在 SRE 与 DevOps 之间的工具和工作区域之间有相当大的重叠。 随着_站点可靠性工作簿_的出现，"SRE 相信与 DevOps 相同，但有略微不同的原因。"

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>用于比较两个操作做法的三种不同方法

SRE 与 DevOps 之间的相似之处明显。 这两个方面的真正有趣之处在于这二者之间的差异。 在这里，我们提供了三种方法来考虑它们之间的关系，这是为此问题提供一些微妙的方法。 您可能不同意这些答复，但每个答案都为讨论提供了一个很好的起点。

### <a name="class-sre-implements-interface-devops"></a>"class SRE implements interface DevOps"

_站点可靠性工作簿_（在[资源簿列表](../resources/books.md)中提到）在其第一章讨论了 SRE 和 DevOps。 本章使用短语 "class SRE implements interface DevOps" 作为其副标题。 这是为了提供建议（使用面向开发人员的短语），可以将 SRE 视为 DevOps 理念的特定实现。 本章节指出，"DevOps 相对无提示地在详细级别运行操作"，而 SRE 的做法更 proscriptive。 对于这两个关系的问题，一个可能的答案就是，可以被视为 DevOps 的许多可能实现之一。

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE 是 DevOps 要传递的可靠性

这种比较只是一个 muddied，因为 SRE 和 DevOps 都有多个定义，但它仍有可能有用。 如果您必须将每个运算练习转换为反映其核心关注点的一个或两个单词，这会产生问题。

如果我们从[站点可靠性工程中心](../index.yml)使用此定义的 SRE：

> 站点可靠性工程是一门工程专业，致力于持续帮助组织实现系统、服务和产品的可靠性级别。

这样就可以很容易地说，SRE 的这个词为 "可靠性"。 在名称中间，还可以提供此声明的一些优秀证据。

如果我们从[Azure DevOps 资源中心](https://docs.microsoft.com/azure/devops/learn/)使用此 DevOps 定义：

> DevOps 是人员、过程和产品的集合体，它让我们可以向最终用户持续提供价值。

那么，类似的升华 for DevOps 可以 "传递"。

因此，"SRE 指的是 DevOps 传递的可靠性"。

### <a name="direction-of-attention"></a>注意方向

这项答案称为 "Thomas Limoncelli" 的 "解释" 和 "" 中所述的 "_查找 SRE_ "[书籍。](../resources/books.md) 他注意到，DevOps 工程师很大程度上侧重于软件开发生命周期管道，其中包含偶尔的生产运营责任，同时 SREs 重点介绍偶尔产生 SDLC 管道责任的生产运营。

但更重要的是，他还在一侧就绘制了一个从软件开发流程开始的图表，而生产操作在另一侧。 这两个连接由为从开发人员获取代码而构建的常用管道进行连接，通过所需的测试和阶段进行 shepherd，然后将该代码迁移到生产中。

Limoncelli 说明 DevOps 工程师从开发环境开始，并自动执行生产过程中的步骤。 完成后，它们会返回以优化瓶颈。

另一方面，SREs 将重点放在生产操作上，并将其深入到管道中，作为一种改善最终结果的方法（基本以相反方向工作）。

这种情况在 SRE 和 DevOps 的方向上非常重要，有助于区分它们。

## <a name="coexistence-in-the-same-organization"></a>同一组织中的共存

我们想要解决的最后一个问题是 "可以在同一组织中同时拥有 SRE 和 DevOps 吗？"

此问题的答案是 emphatic "yes！"。

我们希望前面的答案能让你了解两个操作做法的重叠方式，并且在不重叠的情况下，如何才能获得重点。 具有已建立的 DevOps 实践的组织可以在小规模（例如，尝试 SLIs 和 Slo）上尝试使用 SRE 实践，而不必承诺创建 SRE 位置或团队。 这是一个相当常见的 SRE 采用模式。

## <a name="next-steps"></a>后续步骤

想要了解有关站点可靠性工程或 DevOps 的详细信息？ 请查看我们的[站点可靠性工程中心](../index.yml)和[Azure DevOps Resource center](https://docs.microsoft.com/azure/devops/learn/)。
