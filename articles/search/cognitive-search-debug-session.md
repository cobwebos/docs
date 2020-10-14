---
title: " (预览) 调试会话概念"
titleSuffix: Azure Cognitive Search
description: 调试会话（通过 Azure 门户访问）提供一个类似于 IDE 的环境，可在其中识别和修复错误、验证更改，以及将更改推送到 AI 扩充管道中的技能组。 调试会话当前为预览版。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057614"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>在 Azure 中调试会话认知搜索

调试会话是一个可视化编辑器，可与 Azure 门户中的现有技能组配合使用。 在调试会话中，可以识别和解决错误、验证更改，以及将更改推送到 AI 扩充管道中的生产技能组。

> [!Important]
> 调试会话是一项在提供时未附带服务级别协议的预览功能，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="using-debug-sessions"></a>使用调试会话

启动会话时，服务将创建技能组、索引器和索引的副本，其中使用单个文档来测试技能组。 在会话中进行的更改将保存到会话中。 在调试会话中进行的更改不会影响生产技能组，除非向该技能组提交更改。 提交更改将覆盖生产技能组。

在调试会话期间，可以在特定文档的上下文中编辑技能组、检查和验证扩充树中的每个节点。 解决扩充管道问题后，可将更改保存到会话并提交到生产技能组。 

如果扩充管道没有任何错误，则可使用调试会话来以增量方式扩充文档、测试和验证每个更改，然后再提交更改。

## <a name="creating-a-debug-session"></a>创建调试会话

要启动调试会话，必须已有 AI 扩充管道，且该管道中包括数据源、技能组、索引器和索引。 要配置调试会话，需要为会话命名，并提供一个用于在索引器运行期间缓存技能执行的常规用途存储帐户。 还需要选择将运行的索引器。 索引器存储了对数据源、技能组和索引的引用。 调试会话默认为数据源中的第一个文档，也可以指定数据源中的一个文档用于逐步执行。

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="创建调试会话":::

## <a name="debug-session-features"></a>调试会话功能

调试会话从对选定文档执行技能组开始。 调试会话将记录与技能组中每个操作关联的其他元数据。 元数据通过管道的技能执行创建，可向以下功能集发出通知，然后可使用这些功能帮助识别和解决技能组的问题。

## <a name="ai-enrichments"></a>AI 扩充

随着技能的执行，表示文档的扩充树也会增长。 使用树来直观显示技能或扩充的输出，可全面了解所执行的所有扩充。 可查看整个文档，并检查扩充树的每个节点。 此透视更易于塑造对象。 此格式还提供了树中每个节点的类型、路径和内容的视觉提示。

## <a name="skill-graph"></a>技能图

“技能图”视图提供技能组的分层直观表示形式。 此图按照技能的执行顺序采用从上到下的表示形式。 依赖于其他技能的输出的技能将显示在图中靠近下方的位置。 位于层次结构中同一级别的技能可并行执行。 

在关系图中选择某个技能后，将突出显示与其相连的技能、创建其输入的节点和接受其输出的节点。 每个技能节点都会显示其类型、错误或警告，以及执行计数。 在“技能图”中，可选择要调试或增强的技能。 选择技能后，其详细信息将显示在图右侧的“技能详细信息”窗格中。

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="创建调试会话":::

## <a name="skill-details"></a>技能详细信息

在技能图中突出显示特定技能时，“技能详细信息”窗格将显示一组用于处理该技能的区域。 可以查看和编辑技能设置的详细信息。 提供了技能的 JSON 定义。 还显示了技能执行以及错误和警告的详细信息。 可通过“技能设置”选项卡和“技能 JSON 编辑器”直接编辑技能 。 [`</>`](#expression-evaluator) 可打开一个窗口，用于查看和编辑技能输入和输出的表达式。

“技能设置”窗口中的嵌套输入控件可用于为投影、复杂类型字段的输出字段映射或技能输入生成复杂的形状。 与表达式计算器配合使用时，嵌套输入可提供简单的测试和验证表达式生成器。

## <a name="skill-execution-history"></a>技能执行历史记录

可针对单个文档多次执行一个技能组中的某个技能。 例如，对于从每个文档中提取的每个图像，将执行一次 OCR 技能。 在“技能详细信息”窗格中，“执行”选项卡会显示技能的执行历史记录，有助于更深入地了解每次技能调用。 

使用执行历史记录，可追溯生成特定扩充的技能。 单击某个技能输入，可导航到生成该输入的技能。 通过此操作，可识别下游技能中可能出现的问题的根本原因。 

如果识别了潜在问题，执行历史记录将显示指向生成了特定输入的技能的链接，并提供类似于堆栈跟踪的功能。 单击与输入关联的技能，导航到用于修复任何错误或继续跟踪特定问题的技能。

在生成自定义技能或使用自定义技能调试错误时，可以选择在执行历史记录中生成技能调用请求。

## <a name="enriched-data-structure"></a>扩充数据结构

“扩充数据结构”窗格显示通过技能组实现的文档扩充，详细介绍了每个扩充和起始技能的上下文。 “表达式计算器”也可用于查看每个扩充的内容。

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="创建调试会话":::

## <a name="expression-evaluator"></a>表达式计算器

使用“表达式计算器”，可快速浏览任何路径的值。 它可用于在更新技能或投影的任何输入或上下文之前编辑路径和测试结果。

## <a name="errorswarnings"></a>错误/警告

此窗口显示在调试会话中针对文档执行技能组时生成的所有错误和警告。

## <a name="limitations"></a>限制

调试 sesisons 适用于所有正式可用的数据源 amd 大多数预览版数据源。 当前不支持 (预览) 和 Cassandra API (预览) 的 MongoDB API。

## <a name="next-steps"></a>后续步骤

现在，你已了解调试会话的元素，请尝试根据教程进行实践体验。

> [!div class="nextstepaction"]
> [探索调试会话功能教程](./cognitive-search-tutorial-debug-sessions.md)