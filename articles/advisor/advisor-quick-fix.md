---
title: 顾问建议的快速修复修复
description: 使用顾问中的快速修复执行批量修复
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502485"
---
# <a name="quick-fix-remediation-for-advisor"></a>顾问的快速修复修复
**快速修复**功能支持对多个资源的建议进行更快、更简单的补救方法。 它提供资源批量修复的功能，并帮助您通过大规模资源修复更快地优化订阅。
此功能仅可用于某些建议，可通过 Azure 门户。


## <a name="steps-to-use-quick-fix"></a>使用"快速修复"的步骤

1. 从具有 **"快速修复"** 标签的建议列表中，单击建议。

   ![顾问快速修复](./media/quick-fix-1.png)
   
   *图像中的价格仅供参考*

2. 在"建议详细信息"页上，您将看到具有此建议的资源列表。 选择要为建议进行补救的所有资源。

   ![顾问快速修复](./media/quick-fix-2.png)
   
   *图像中的价格仅供参考*

3. 选择资源后，单击 **"快速修复"** 按钮进行批量修复。

   > [!NOTE]
   > 某些列出的资源可能会被禁用，因为您没有修改它们的适当权限。
   
   > [!NOTE]
   > 如果存在其他影响，除了 Advisor 中提到的好处外，还将在体验中通知您，以帮助您做出明智的补救决策。
   
4. 您将收到有关修复完成的通知。 如果资源列表视图中存在未修复的资源和资源处于所选模式，则会看到错误。  


## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](https://docs.microsoft.com/rest/api/advisor/)
