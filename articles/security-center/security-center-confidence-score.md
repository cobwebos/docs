---
title: Azure 安全中心的置信度评分 | Microsoft Docs
description: " 了解如何使用 Azure 安全中心置信度评分。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60916555"
---
# <a name="alert-confidence-score"></a>警报置信度分数 

在 Azure 安全中心可以查看 Azure 中运行的各个资源，以及在安全中心检测到潜在问题时发出的警报。 安全运营团队可能难以逐个解决警报列中的每个警报，因此有必要设定需要调查的警报的优先级。 警报调查工作可能很复杂且很耗时，因此需要忽略一些警报。

安全中心内的置信度评分可以帮助团队会审警报并设定其优先级。 安全中心自动运用行业最佳做法、智能算法和分析师使用的流程来确定某个威胁是否合法，并以置信度评分的形式提供有意义的见解。

## <a name="how-the-confidence-score-is-triggered"></a>置信度评分的触发方式

检测到有可疑的进程在虚拟机上运行时，会生成警报。 安全中心针对 Azure 中运行的 Windows 虚拟机评审并分析这些警报。 它会使用高级算法，针对组织中的多个实体和数据源以及所有的 Azure 资源执行自动检查与关联，并提供一个置信度评分，用于表示安全中心有多大把握认为该警报符合事实且需要调查。

## <a name="understanding-the-confidence-score"></a>了解置信度评分

置信度评分范围介于 1 和 100 之间，表示安全中心有多大的把握认为该警报需要调查。 评分越高，就表示安全中心越有把握地认为该警报指出了真正的恶意活动。 置信度评分包括一个列表，其中列出了为该警报提供该置信度评分的首要原因。 置信度评分可让安全分析师轻松设定其对警报采取的应对措施的优先级，首先解决影响最大的攻击，最终减少应对攻击和漏洞所需的时间。

查看置信度评分：
- 在安全中心门户中，打开“安全警报”边栏选项卡。
-  警报和事件从高到低进行组织，这意味着，安全中心越有把握地认为某个警报反映了某种威胁，该警报就越靠近页面顶部。 


 ![置信度分数][1]

查看安全中心给出警报置信度时所依据的数据：
- 在“安全警报”边栏选项卡中的“置信度”下，查看给出该置信度评分所依据的观测结果，并获取与警报相关的见解。  这可以更深入地了解导致该警报的活动性质。

  ![可疑的置信度评分][2]

使用安全中心的置信度评分可在环境中设定警报会审的优先级。 置信度评分能够节省时间和精力，因为它可以自动调查警报、运用行业最佳做法和智能算法，并可充当虚拟分析师来确定哪些威胁是真实的并需要重点关注。


## <a name="next-steps"></a>后续步骤
本文介绍了如何使用置信度评分来设定警报调查的优先级。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
