---
title: Azure Log Analytics 中的自定义字段 | Microsoft Docs
description: Log Analytics 的“自定义字段”功能，使你可以基于添加到已收集记录的属性的 Log Analytics 记录创建自己的可搜索字段。  本文介绍了创建自定义字段的过程，并通过示例事件提供详细的演练。
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 164b071a47c13f7c5586bd287adc41bea54a1198
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129611"
---
# <a name="custom-fields-in-log-analytics"></a>Log Analytics 中的自定义字段
Log Analytics 的**自定义字段**功能使你可以通过添加自己的可搜索字段来扩展 Log Analytics 中的现有记录。  自定义字段会自动填充，填充的数据从同一记录的其他属性中提取。

![自定义字段概述](media/log-analytics-custom-fields/overview.png)

例如，以下示例记录的事件描述中隐藏着有用记录。  将此数据提取到单独的属性中，就可以在排序和筛选等操作中对其进行使用。

![日志搜索按钮](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> 在预览版的工作区中，限制使用 100 个自定义字段。  正式发布此功能时，会扩展该限制。
> 
> 

## <a name="creating-a-custom-field"></a>创建自定义字段
创建自定义字段时，Log Analytics 必须了解应该使用哪些数据填充其值。  将使用由 Microsoft Research 开发的 FlashExtract 技术来快速找出此数据。  不需要你提供确切说明，Log Analytics 就会获知要从所提供示例中提取的数据。

以下各节提供了创建自定义字段的步骤。  本文末尾部分提供了示例提取的演练。

> [!NOTE]
> 将匹配指定条件的记录添加到 Log Analytics 时，会填充自定义字段，因此它将仅显示在创建自定义字段后收集的记录上。  创建自定义字段时，不会将该字段添加到数据存储中已存在的记录中。
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>步骤 1 – 确定将具有自定义字段的记录
第一步是确定会获得自定义字段的记录。  首先执行[标准日志搜索](log-analytics-log-searches.md)，然后选择要充当模型的记录，Log Analytics 将通过该模型进行学习。  当指定要将数据提取到自定义字段中时，“字段提取向导”会打开，可以在其中验证和优化条件。

1. 转到“日志搜索”，然后使用[查询来检索记录](log-analytics-log-searches.md)（将具有自定义字段的记录）。
2. 选择 Log Analytics 将用作模型的记录，以便 Log Analytics 学习如何提取要填充到自定义字段中的数据。  确定要从该记录中提取的数据，然后 Log Analytics 将使用此信息为所有类似记录确定自定义字段的填充逻辑。
3. 单击该记录的任何文本属性左侧的按钮，并选择“字段提取自”。
4. 将打开“字段提取向导”，所选记录会显示在“主示例”列中。  将为所选属性中具有相同值的记录定义自定义字段。  
5. 如果所选内容不完全是所需要的，可选择其他字段来缩小条件范围。  要更改条件的字段值，必须先取消，然后选择匹配所需条件的其他记录。

### <a name="step-2---perform-initial-extract"></a>步骤 2 - 执行初始提取。
在完成确定将具有自定义字段的记录后，请确定要提取的数据。  Log Analytics 将使用此信息在类似记录中确定类似模式。  在下一个步骤中，将可以验证结果，并提供更多详细信息供 Log Analytics 在其分析中使用。

1. 突出显示示例记录中要用于填充自定义字段的文本。  然后会显示一个对话框，用于命名字段并执行初始提取。  将自动附加字符 **\_CF**。
2. 单击“提取”以执行已收集记录的分析。  
3. “摘要”和“搜索结果”部分会显示提取的结果，使你可以检查提取的准确性。  “摘要”显示用于确定记录的条件以及已确定的每个数据值的计数。  “搜索结果”提供匹配条件的记录的详细列表。

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>步骤 3 - 验证提取的准确性并创建自定义字段
完成执行初始提取后，Log Analytics 将基于已收集的数据显示提取结果。  如果觉得结果准确，可以创建自定义字段（无需进行更多工作）。  如果结果不准确，可以优化结果，以便 Log Analytics 可以改善其逻辑。

1. 如果初始提取中的任何值都不正确，可以单击不准确记录旁边的“编辑”图标，并选择“修改此突出显示”修改所选项。
2. 会将该条目复制到“主示例”下方的“其他示例”部分。  若要帮助 Log Analytics 了解它应做的选择，可以在此处调整突出显示。
3. 单击“提取”即可使用此新信息评估现有的所有记录。  基于这个新智能，可以为刚刚修改之外的记录修改结果。
4. 继续添加修正，直到提取中的所有记录都正确标识要用于填充新自定义字段的数据。
5. 如果对结果满意，请单击“保存提取”。  自定义字段现已定义，但它还不会添加到任何记录。
6. 等待收集匹配指定条件的新记录，再次运行日志搜索。 新记录应具有自定义字段。
7. 像任何其他记录属性一样使用自定义字段。  可以将它用于聚合和分组数据，甚至可以将它用于生成新的见解。

## <a name="viewing-custom-fields"></a>查看自定义字段
可以在 Azure 门户中通过 Log Analytics 工作区的“高级设置”菜单，查看管理组中的所有自定义字段的列表。  依次选择“数据”、“自定义字段”就可以获取工作区中所有自定义字段的列表。  

![自定义字段](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>删除自定义字段
有两种方法可以删除自定义字段。  第一种方法是在查看上述完整列表时，对每个字段使用“删除”选项。  另一种方法是检索记录，并单击字段左侧的按钮。  菜单中将有一个选项可用于删除自定义字段。

## <a name="sample-walkthrough"></a>示例演练
以下部分演示创建自定义字段的完整示例。  此示例会提取指示服务更改状态的 Windows 事件中的服务名称。  这依赖于服务控制管理器在 Windows 计算机的系统日志中创建的事件。  如果想要遵循此示例，必须[收集系统日志的信息事件](log-analytics-data-sources-windows-events.md)。

我们输入以下查询从服务控制管理器中返回事件 ID 为 7036 的所有事件（即指示服务启动或停止的事件）。

![查询](media/log-analytics-custom-fields/query.png)

然后，我们选择事件 ID 为 7036 的任一记录。

![源记录](media/log-analytics-custom-fields/source-record.png)

我们需要 **RenderedDescription** 属性中出现的服务名称，并选择该属性旁边的按钮。

![提取字段](media/log-analytics-custom-fields/extract-fields.png)

将打开“字段提取向导”，请在“主示例”列中选择 **EventLog** 和 **EventID** 这两个字段。  这表示将为系统日志中事件 ID 为 7036 的事件定义自定义字段。  这已够用，因此我们不需要选择任何其他字段。

![主示例](media/log-analytics-custom-fields/main-example.png)

我们突出显示 **RenderedDescription** 属性中的服务的名称，并使用 **Service** 标识服务名称。  自定义字段将称为 **Service_CF**。

![字段标题](media/log-analytics-custom-fields/field-title.png)

我们会看到：对于某些记录，已正确标识服务名称；但对于其他记录，则并未正确标识服务名称。   “搜索结果”显示：未选择“WMI 性能适配器”的部分名称。  “摘要”显示 **DPRMA** 服务的四条记录错误地包含了额外的单词，两条记录识别了“模块安装程序”而不是“Windows 模块安装程序”。  

![搜索结果](media/log-analytics-custom-fields/search-results-01.png)

我们从“WMI 性能适配器”记录开始。  单击该记录的编辑图标，并单击“修改此突出显示”。  

![修改突出显示](media/log-analytics-custom-fields/modify-highlight.png)

我们会增加突出显示以包括单词 **WMI**，并重新运行该提取。  

![其他示例](media/log-analytics-custom-fields/additional-example-01.png)

我们可以看到：“WMI 性能适配器”的条目已得到修正，Log Analytics 也使用该信息来修正“Windows 模块安装程序”的记录。  我们还可以通过“摘要”部分进行查看，即使 **DPMRA** 仍未正确识别。

![搜索结果](media/log-analytics-custom-fields/search-results-02.png)

滚动到含有 DPMRA 服务的记录，并采用相同过程对该记录进行修正。

![其他示例](media/log-analytics-custom-fields/additional-example-02.png)

 运行该提取后，我们可以看到：所有结果现在都已准确。

![搜索结果](media/log-analytics-custom-fields/search-results-03.png)

我们可以看到：**Service_CF** 已创建，但尚未添加到任何记录中。

![初始计数](media/log-analytics-custom-fields/initial-count.png)

一段时间过后，新事件已完成收集，可以看到 Service_CF 字段现已添加到与条件匹配的记录中。

![最终结果](media/log-analytics-custom-fields/final-results.png)

现在，我们可以像任何其他记录属性一样使用自定义字段。  为了说明这一点，我们会创建按新的 **Service_CF** 字段分组的查询，来检查哪些服务最活跃。

![按查询分组](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)，使用自定义字段作为条件生成查询。
* 监视使用自定义字段分析的[自定义日志文件](log-analytics-data-sources-custom-logs.md)。

