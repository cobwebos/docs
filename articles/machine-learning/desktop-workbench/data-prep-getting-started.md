---
title: 开始使用 Azure 机器学习的数据准备 | Microsoft Docs
description: 这是 AML Workbench 数据准备部分的入门指南
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="getting-started-with-data-preparation"></a>数据准备入门

欢迎使用数据准备入门指南。 

数据准备提供了一套用于有效地浏览、了解和解决数据中的问题的工具。 它允许你以多种形式使用数据，并将数据转换为更适合下游使用的已清理数据。

数据准备作为 Azure Machine Learning Workbench 体验的一部分安装。  在本地使用数据准备或作为运行时或执行环境部署到目标群集和云。

设计时间运行时将 Python 用于扩展性，具体取决于 Pandas 等各种 Python 库。 与 Azure ML Workbench 的其他组件一样，没有必要安装 Python，已为你安装。 但是如果你需要安装其他库，则需要将这些库安装在 Azure ML Workbench Python 目录中，而不是安装在常用的 Python 目录。 可以在[此处](data-prep-python-extensibility-overview.md)找到有关如何安装程序包的更多详细信息。

在使用数据准备之前需要一个项目，一旦创建这个项目，你就可以准备数据了。 

通过选择屏幕左侧的数据图标![数据源图标](media/data-prep-getting-started/data-source-icon.png)导航到项目的“数据”部分。  再次单击“+”来“添加数据源”。 应会启动“数据源”向导，并在完成向导后会向项目添加数据源 (.dsource) 文件。 默认情况下，数据视图为网格。 在网格上方，可能还可以选择“指标”视图。 “指标”视图中显示了摘要统计信息。  在查看摘要统计信息后，单击屏幕顶部的“准备”。 [有关数据源向导的详细信息](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>构建数据准备基块 ##
### <a name="the-package"></a>程序包 ###

程序包是工作的主容器。 程序包是保存到磁盘并从中加载的项目。 在客户端内部运行时，会在后台对程序包不断进行自动保存。 

可以在 Python 中或通过 Jupyter 笔记本了解程序包输出/结果。

可以跨多个运行时执行包，包括本地 Python、Spark（包含在 Docker 中），以及 HDInsight。

一个程序包包含一个或多个数据流，这些数据流是应用于数据的步骤和转换。

一个包可以使用另一个包作为数据源（称为引用数据流）。

### <a name="the-dataflow"></a>数据流 ###
数据流都有一个源和可选的转换，这些转换通过一系列步骤和可选目标进行排列。 首先单击一个步骤来重新执行所有源和转换并包含所选步骤。  通过该步骤转换的数据显示在网格中。 可以通过“步骤列表”添加、移动和删除数据流中的步骤。

可以打开和关闭客户端右侧的“步骤列表”，以提供更大的屏幕空间。

UI 中可以同时存在多个数据流，每个数据流都表示为 UI 中的选项卡。

### <a name="the-source"></a>源
源是数据来源，以及数据所采用的格式。 数据准备包的数据始终来源于另一个数据流（数据流）。 它是此包含信息的引用数据流。 每个源都具有不同的用户体验，以允许对其进行配置。 源生成数据的“矩形”/表格视图。 如果源数据最初存在“右侧未对齐”，则结构会被规范化为“矩形”。 [附录 2 提供受支持源的当前列表](data-prep-appendix2-supported-data-sources.md)。

### <a name="the-transform"></a>转换 ###
转换使用给定格式的数据、对数据执行某些操作（如更改数据类型），然后生成新格式数据。 每个转换都有其自己的 UI 和行为。 通过数据流中的步骤将几个转换链接在一起是数据准备功能的核心。 [附录 3 提供受支持转换的当前列表](data-prep-appendix3-supported-transforms.md)。

### <a name="the-inspector"></a>检查器 ###

检查器是数据的可视化效果，并可用于提高对数据的理解。  了解数据和数据质量问题可帮助你决定应执行哪些操作（转换）。 某些检查器支持生成转换的操作。 例如，可通过值计数检查器选择一个值，然后应用筛选器以包含或排除该值。 检查器还可以为转换提供上下文。 例如，选择一个或多个列更改可以应用的可能转换。

一个列可能在任何时候都拥有多个检查器（例如，列统计信息和直方图）。 还有跨多个列的检查器实例。 例如，所有数字列可能同时拥有直方图。

检查器会显示在屏幕底部的 Profiling Well 中。  最大化检查器以在主要内容区域内放大查看。 将数据网格视为默认检查器。 可以将任何检查器扩展到主要内容区域。 主要内容区域中的检查器会最小化到 Profiling Well。 通过单击该检查器中的铅笔图标来自定义检查器。 在 Well 中通过拖放重新排序检查器。

某些检查器支持“Halo”模式。 在应用最后一个转换之前，该模式会显示值或状态。 旧值显示为灰色，当前值显示为前景，并显示转换的影响。 [附录 4 提供受支持检查器的当前列表](data-prep-appendix4-supported-inspectors.md)。

### <a name="the-destination"></a>目标
 目标是在数据流中准备好数据后在其中写入/导出数据的位置。 给定的数据流可以有多个目标。 每个源都有不同的用户体验，以允许对其进行配置。 目标使用“矩形”/表格格式的数据，并将它以给定格式写出到某个位置。 [附录 5 提供受支持目标的当前列表](data-prep-appendix5-supported-destinations.md)。

### <a name="using-data-preparation"></a>使用数据准备 ###
数据准备假设使用基本五步方法论/方法进行数据准备。

#### <a name="step-1-ingestion"></a>步骤 1：引入 ####
使用项目视图中的“添加数据源”选项导入用于数据准备的数据。  所有数据的初始引入都通过数据源向导处理。

#### <a name="step-2-understandprofile-the-data"></a>步骤 2：了解/简析数据 ####

首先，查看各列顶部的数据质量栏。 绿色表示具有值的行。 灰色表示缺少值、值为 null 等的行。红色表示错误值。 将鼠标悬停在栏上以获得工具提示，其中每三个 Bucket 中的行数都是准确的。 数据质量栏使用一个对数刻度，因此总是要检查实际数值，以粗略感受丢失的数据量。

![列](media/data-prep-getting-started/columns.png)

接下来，将其他检查器和网格组合使用，以更好地了解数据特征。  启动构建与进行进一步分析所需的数据准备相关的假设。 大多数检查器适用于单个列或少量列。  

很可能需要跨多个列的多个检查器来理解数据。 可以通过 Profiling Well 中的各种检查器进行滚动。 在 Well 中，还可以将检查器移到列表的开头，以便在立即可视区域中查看它们。

![检查器](media/data-prep-getting-started/inspectors.PNG)

为连续和分类变量/列提供了不同检查器。 检查器菜单的启用和禁用选项取决于你拥有的变量/列类型。

在使用具有多列的宽数据集时，建议使用子集这一有效方法。 此方法包括关注少量的列（例如，5-10）、准备它们，然后处理剩余的列。 网格检查器支持列的垂直分区，因此如果你有 300 多个列，则需要对它们进行“分页”。
 

#### <a name="step-3-transform-the-data"></a>步骤 3：转换数据 ####
转换更改数据并允许执行数据，以支持当前的工作假设。 转换作为“步骤”显示在“步骤列表”右侧。 可以通过单击“步骤列表”中的任意点“按时间顺序查看”步骤列表。

给定“步骤”左侧的绿色图标表示它已运行，数据反映转换的执行。 “步骤”左侧的垂直条指示检查器中数据的当前状态。

![steps](media/data-prep-getting-started/steps.PNG)

尝试对数据进行小频率更改，并在每次更改之后进行验证（步骤 4），因为假设会发生变化。

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>步骤 4：验证转换的影响。 
决定假设是否正确。 如果正确无误，则制定下一步假设，并为新的假设重复步骤 2-3。 如果不正确，则撤消上一个转换，制定新的假设并重复步骤 2-3。

确定转换是否有相应影响的主要方法是使用检查器。 使用现有检查器。 使用已启用 Halo 效果的检查器或启动多个检查器来查看给定时间点的数据。

![halo 检查器](media/data-prep-getting-started/halo1.PNG) ![halo 检查器](media/data-prep-getting-started/halo2.PNG)

若要撤消转换，请访问 UI 右侧的“步骤列表”。 （可能需要回弹出“步骤列表”面板。若要打开它，请单击指向左侧的双 V 形图标）。 在面板中，选择要撤消的已执行转换。 选择 UI 块右侧的下拉列表。 选择“编辑”进行更改，或选择“删除”从“步骤列表”和“数据流”中删除转换。

#### <a name="step-5-output"></a>步骤 5：输出 
在完成数据准备后，可以将数据流写入输出。 数据流可以有多个输出。 从“转换”菜单中，可以选择要将数据集写入为哪个输出。 还可以选择输出目标。 

## <a name="list-of-appendices"></a>附录列表 
[附录 2 - 支持的数据源](data-prep-appendix2-supported-data-sources.md)  
[附录 3 - 支持的转换](data-prep-appendix3-supported-transforms.md)  
[附录 4 - 支持的检查器](data-prep-appendix4-supported-inspectors.md)  
[附录 5 - 支持的目标](data-prep-appendix5-supported-destinations.md)  
[附录 6 - Python 中的示例筛选表达式](data-prep-appendix6-sample-filter-expressions-python.md)  
[附录 7 - Python 中的示例转换数据流表达式](data-prep-appendix7-sample-transform-data-flow-python.md)  
[附录 8 - Python 中的示例数据源](data-prep-appendix8-sample-source-connections-python.md)  
[附录 9 - Python 中的示例目标连接](data-prep-appendix9-sample-destination-connections-python.md)  
[附录 10 - Python 中的示例列转换](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>另请参阅

[高级数据准备教程](tutorial-bikeshare-dataprep.md)