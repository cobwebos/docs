---
title: 针对顾问建议的快速修复修正
description: 使用顾问中的快速修复执行批量修正
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968589"
---
# <a name="quick-fix-remediation-for-advisor"></a>顾问的快速修复修正
通过**快速修复**，可以更快、更轻松地根据建议对多个资源进行修正。 它提供了对资源批量修正的功能，并通过对资源进行大规模修正来帮助你更快地优化订阅。
该功能可以通过 Azure 门户使用，但只能用于某些建议。


## <a name="steps-to-use-quick-fix"></a>使用“快速修复”的步骤

1. 在带有“快速修复”标签的建议列表中，单击建议。

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Azure Advisor 屏幕截图，其中显示了建议中的 &quot;快速修复&quot; 标签。":::
   
   图像中的价格仅用于示例

2. 在“建议详细信息”页上，你会看到具有此建议的资源的列表。 选择要根据此建议进行修正的所有资源。

   :::image type="content" source="./media/quick-fix-2.png" alt-text="{Azure Advisor 屏幕截图，其中显示了建议中的 &quot;快速修复&quot; 标签。":::
   
   图像中的价格仅用于示例

3. 选择资源后，请单击“快速修复”按钮进行批量修正。

   > [!NOTE]
   > 列出的某些资源可能已禁用，因为你没有修改它们的相应权限。
   
   > [!NOTE]
   > 至于其他含义，除了顾问中提到的权益外，你还会在体验中进行交流，这样可以更好地做出明智的修正决策。
   
4. 修正完成后，你会收到通知。 如果资源列表视图中有未修正的资源和处于选定模式的资源，则会出现错误。  


## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)
