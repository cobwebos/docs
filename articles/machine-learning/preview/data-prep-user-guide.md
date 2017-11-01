---
title: "有关如何使用 Azure 机器学习数据准备的深度指南 | Microsoft Docs"
description: "本文档提供有关如何使用 Azure 机器学习数据准备解决数据问题的概述和详细信息"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 1a1e12dbb5e32f62266ee6a3cdca9e781569e58c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="data-preparations-user-guide"></a>数据准备用户指南 
Azure 机器学习数据准备体验提供大量的丰富功能。 本文阐述该体验的最深入部分。

### <a name="step-execution-history-and-caching"></a>步骤执行、历史记录和缓存 
出于性能原因，数据准备步骤历史记录保留一系列缓存。 如果选择一个步骤，它会命中不会重新执行的缓存。 如果在步骤历史记录的末尾有一个写入块，并且在步骤中来回切换，但是没有做任何更改，那么第一次之后不会触发写入操作。 在以下情况下，会发生新的写入，并覆盖以前的写入：

- 更改写入块。
- 添加新的转换块并将其移动到生成缓存失效的写入块上方。
- 更改生成缓存失效的写入块上方块的属性。
- 在示例中选择刷新（因此使所有缓存失效）。

### <a name="error-values"></a>错误值

数据转换可能会因为输入值而失败，因为无法对该值进行适当地处理。 例如，为执行类型强制转换操作，如果输入的字符串值不能强制转换为指定的目标类型，则强制转换失败。 类型强制转换操作可能会将字符串类型的列转换为数值或布尔类型，尝试复制不存在的列也会出现此情况。 （将“删除列 X”操作移到“复制列 X”操作前面会发生这种失败）。

在这些情况下，数据准备会生成错误值作为输出。 错误值指示针对给定值的上一个操作失败了。 在内部，它们被视为第一类值类型，但它们的存在不会更改列的基础类型，即使某一列完全由错误值组成。

错误值很容易识别。 它们以红色突出显示为“错误”。 为确定错误的原因，请将鼠标悬停在错误值上，以查看失败的文本说明。

错误值传播。 发生错误值后，它在大多数情况下会作为一个错误在大多数操作中进行传播。 可通过三种方式来替换或删除它们：

* 将
    -  右键单击列并选择“替换错误值”。 然后，你可以选择在列中发现的每个错误值的替换值。

* 删除
    - 数据准备包含交互式筛选器，以便保留或删除错误值。
    - 右键单击列并选择“筛选列”。 若要保留或删除错误值，请创建条件为“is error”或“is not error”的条件语句。

* 使用 Python 表达式来有条件地操作错误值。 有关详细信息，请参阅[有关 Python 扩展的部分](data-prep-python-extensibility-overview.md)。

### <a name="sampling"></a>采样
数据源文件从本地文件系统或远程位置接收来自一个或多个源的原始数据。 示例块允许你指定是否通过生成示例来处理数据子集。 在后续步骤中执行操作时，选择示例数据而不是大型数据集进行操作通常会带来更出色的性能。

对于每个数据源文件，可以生成并存储多个示例。 但是只有一个示例可以设置为活动示例。 你可以在“数据源”向导中或通过编辑示例块来创建、编辑或删除示例。 引用数据源的任何数据准备文件本质上使用数据源文件中指定的示例。

有大量的采样策略可供使用，每个都有不同的可配置参数。

#### <a name="top"></a>上限
此策略适用于本地或远程文件。 它将前 N 行（由计数指定）放入到数据源中。

#### <a name="random-n"></a>随机 N 
此策略仅适用于本地文件。 它将随机的 N 行（由计数指定）放入到数据源中。 可以提供特定的种子以确保生成相同的示例，前提是计数也相同。

#### <a name="random-"></a>随机百分比 
此策略适用于本地或远程文件。 在两个事例中，必须提供概率和种子，类似于随机 N 策略。

有关远程文件的示例，还需要提供其他参数。

- 样本生成器 
  - 选择一个 Spark 群集或远程 Docker 计算目标，以便用于生成示例。 必须事先为项目创建计算目标，才能在此列表中显示。 按照[如何在 Azure 机器学习中使用 GPU](how-to-use-gpu.md) 的“创建新的计算目标”中的步骤来创建计算目标。
- 样本存储 
  - 提供一个中间存储位置来存储远程示例。 此路径必须是与输入文件位置不同的目录。

#### <a name="full-file"></a>整个文件 
如果将完整文件放入到数据源中，则此策略仅适用于本地文件。 如果该文件过大，则此选项可能会减慢应用程序中的未来操作。 可能发现更适合使用其他采样策略。


### <a name="fork-merge-and-append"></a>分支、合并和追加

当通过数据集应用筛选器，此操作会将数据拆分为两个结果集，其中一个集表示筛选器中的成功记录，另一个集则表示失败的记录。 在任一情况下，用户都可以选择要显示的结果集。 用户可以放弃其他数据集，或将其放在新的数据流中。 后一种方式称为分支。

分支： 
1. 选择列，单击鼠标右键，并选择“筛选”列。

2. 在“我想要”下，选择“保留行”以显示通过筛选的结果集。

3. 选择“删除行”显示失败的结果集。

4. 在“条件”的后面，选择“创建包含筛选出的行的数据流”，以将非显示结果集分支到新数据流。


这种做法通常用于分离一组需要额外准备的数据。 在处理分支数据集之后，通常会将数据与原始数据流中的结果集合并。 若要执行“合并”（与“分支”操作相反），请使用以下操作之一：

- 追加行。 垂直合并两个或多个数据流（按行）。 
- 追加列。 水平合并两个或多个数据流（按列）。


>[!NOTE]
>如果发生列冲突，“追加列”操作将会失败。


完成合并操作之后，源数据流将引用一个或多个数据流。 数据准备会通过步骤列下方应用程序右下角的通知来通知你。


引用数据流中的任何操作都将要求父数据流刷新引用数据流中所使用的示例。 在这种情况下，确认对话框会替换右下角的数据流引用通知。 该对话框确认你需要刷新数据流，以同步对任何依赖项数据流的更改。

### <a name="list-of-appendices"></a>附录列表 
* [支持的数据源](data-prep-appendix2-supported-data-sources.md)  
* [支持的转换](data-prep-appendix3-supported-transforms.md)  
* [支持的检查器](data-prep-appendix4-supported-inspectors.md)  
* [支持的目标](data-prep-appendix5-supported-destinations.md)  
* [Python 中的筛选表达式示例](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Python 中的示例转换数据流表达式](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Python 中的示例数据源](data-prep-appendix8-sample-source-connections-python.md)  
* [Python 中的目标连接示例](data-prep-appendix9-sample-destination-connections-python.md)  
* [Python 中的列转换示例](data-prep-appendix10-sample-custom-column-transforms-python.md)  
