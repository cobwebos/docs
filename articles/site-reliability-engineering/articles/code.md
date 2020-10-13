---
title: 常见问题解答： SRE 和编码 |Microsoft Docs
titleSuffix: Azure
description: 常见问题解答：了解 SRE 与编码之间的关系
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089065"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>常见问题：我是否需要知道如何编写代码来处理 SRE？

当个人正在考虑在 SRE 中参与，并且团队正在考虑使用 SRE 实践时，出现的一个常见问题是 "是否需要知道如何编码？"

简短答案：是。 

但完整答案有点微妙。 让我们看一下三个位置，其中的编码进入站点可靠性工程，同时提供每个位置所需的编码专业知识水平。 此列表并不完整，但这些方案是一些更常见的用例。

## <a name="scenario-1-removing-toil-through-automation"></a>方案1：通过自动化删除 toil

站点可靠性工程师和使用 SRE 做法尝试的其他人可能会删除 toil。 "Toil" 表示 SRE 中的特定内容。 Toil 是指由具有特定特征的人工完成的操作。 “辛劳”没有长期补偿价值。 它不会以任何有意义的方式推进服务。 它通常是重复操作并且主要是手动操作（即使可以自动化）。 当服务或系统随着时间的推移规模变大后，对该系统的请求数量也可能按比例增加，并且需要更多的手工劳动。

例如，如果某项服务要求 SRE 团队每周重置某个内容，或者手动预配新的帐户和磁盘空间，或者手动重新启动它，则这是 toil 的操作负载。 完成这些操作并没有使服务以任何长期、持久的方式得到改善。 这些操作可能必须一遍又一遍地重复。

SRE 无法接受辛劳。 他们努力在可能且适当的时候消除辛劳。 这是自动化在 SRE 中发挥作用的地方之一。 如果可自动处理这些请求，则会释放团队，以处理更多的有影响力。

*编码专业知识*：自动化需要某些编码专业知识，但不需要完全的软件工程技巧。 如果可以 (可能在 PowerShell 或 Bourne shell 中编写小型脚本) 或者即使你创建了 [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) 几乎任何代码，此应用仍可帮助消除 toil。

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>方案2：通过 Api/域特定语言 (Dsl) /templates 的控制

虽然对于 SRE 工作而言不是必需的，但能够通过 Api、Dsl 和模板控制环境 (尤其是云环境) 允许 SREs 扩展其工作量。 通过编码，预配/取消预配基础结构、配置监视和集成多项服务都变得更加高效。

*编码专业知识*：与前面的方案类似，这需要一些编码专业知识，但它不需要完全的软件工程技巧。 除了前面提到的脚本和逻辑应用外， [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) 还可以使用最少的编码体验。

## <a name="scenario-3-fixing-the-code"></a>方案3：修复代码

站点可靠性工程师希望提高系统的可靠性。 这种目标有时需要深入到系统的源代码中，确定问题，并经常向代码库进行修复。 尽管此项工作的复杂程度可能因情况而异，但在这种情况下，编码专业知识是一项明确的要求。

*编码专业知识*：在这种情况下，通常需要完整的软件工程专业知识。


## <a name="next-steps"></a>后续步骤

想要了解有关站点可靠性工程和低代码工作的详细信息？ 查看我们的 [站点可靠性工程中心](../index.yml)，以上链接的产品文档。
