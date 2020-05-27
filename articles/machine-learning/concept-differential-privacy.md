---
title: 通过 WhiteNoise 包实现差异隐私
titleSuffix: Azure Machine Learning
description: 了解什么是差异隐私，以及 WhiteNoise 包如何帮助你实现保留数据隐私的差异隐私系统。
author: luisquintanilla
ms.author: luquinta
ms.date: 05/03/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.openlocfilehash: 09ac31f31f42f5aed9e7dd464e1fce1436cfe581
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594986"
---
# <a name="preserve-data-privacy-by-using-differential-privacy-and-the-whitenoise-package"></a>使用差异隐私和 WhiteNoise 包来保留数据隐私

了解什么是差异隐私，以及 WhiteNoise 包如何帮助你实现差异隐私系统。

随着组织收集并用于分析的数据量的增加，隐私和安全问题也逐渐增多。 分析需要数据。 通常，用于训练模型的数据越多，模型就越精确。 当个人信息用于这些分析时，在整个使用过程中数据保密尤为重要。

## <a name="how-differential-privacy-works"></a>差异隐私的工作原理

差异隐私是一组系统和做法，可帮助保持个人数据的安全和隐私。

> [!div class="mx-imgBorder"]
> ![差异隐私过程](./media/concept-differential-privacy/differential-privacy-process.jpg)

在传统场景中，原始数据存储在文件和数据库中。 用户分析数据时，通常使用原始数据。 这是一个问题，因为它可能会侵犯个人隐私。 差异隐私尝试通过对数据添加“干扰”或随机性来处理此问题，这样用户就无法识别任何单个数据点。 至少，此类系统提供了合理的可否认性。

在差异隐私系统中，可通过称为“查询”的请求来共享数据。 当用户提交数据查询时，称为“隐私机制”的操作将向请求的数据添加干扰。 隐私机制返回近似数据，而不是原始数据。 此隐私保留结果出现在报表中。 报表包含两个部分：计算的实际数据，以及有关如何创建数据的说明。

## <a name="differential-privacy-metrics"></a>差异隐私指标

差异隐私尝试防止用户产生数量不定的报表，而最终泄露敏感数据。 称为“epsilon”的值度量报表的干扰或隐私程度。 epsilon 与干扰或隐私的关系是相反的。 epsilon 越低，数据的干扰（以及隐私）程度就越高。

epsilon 值为非负数。 小于 1 的值提供了完全合理的可否认性。 任何大于 1 的值会导致公开实际数据的风险更高。 实现差异隐私系统时，你会希望生成一个 epsilon 值介于 0 和 1 之间的报表。

与 epsilon 直接关联的另一个值是 delta。 delta 度量报表不具备完整隐私性的概率。 delta 越高，epsilon 就越高。 由于这些值是关联的，因此使用 epsilon 的频率更高。

## <a name="privacy-budget"></a>隐私预算

为了确保允许多个查询的系统中的隐私，差异隐私定义了速率限制。 此限制称为“隐私预算”。 隐私预算分配了一个 epsilon 值，通常介于 1 和 3 之间，以限制重新识别的风险。 在生成报表时，隐私预算将跟踪单个报表的 epsilon 值以及所有报表的汇总值。 在隐私预算用完或用尽后，用户将无法再访问数据。  

## <a name="reliability-of-data"></a>数据的可靠性

虽然保护隐私应该是我们的目标，但是当涉及到数据的可用性和可靠性时，就需要进行权衡。 在数据分析中，准确性可以被视为对采样误差带来的不确定性的度量。 这种不确定性往往在一定范围内。 从差异隐私角度来看，准确性改为衡量数据的可靠性，而可靠性受到隐私机制所引入的不确定性的影响。 简而言之，更高级别的干扰或隐私会转换为具有较低 epsilon、准确性和可靠性的数据。 尽管数据具有更高隐私性，但它不可靠，因此被使用的可能性就越小。

## <a name="implementing-differentially-private-systems"></a>实现差异隐私系统

实现差异隐私系统较为棘手。 WhiteNoise 是一个开源项目，其中包含用于构建全局差异隐私系统的不同组件。 WhiteNoise 由以下顶级组件组成：

- 核心
- 系统

### <a name="core"></a>核心

核心库包含以下用于实现差异隐私系统的隐私机制：

|组件  |说明  |
|---------|---------|
|分析     | 任意计算的图形说明。 |
|验证程序     | 一个包含一组工具的 Rust 库，这些工具用于检查和派生使分析具有差异隐私性的必要条件。          |
|运行时     | 要执行分析的介质。 引用运行时是用 Rust 编写的，但运行时可以使用任何计算框架（如 SQL 和 Spark）编写，这取决于你的数据需求。        |
|绑定     | 用于生成分析的语言绑定和帮助程序库。 目前 WhiteNoise 提供 Python 绑定。 |

### <a name="system"></a>系统

系统库提供了以下工具和服务，用于处理表格数据和关系数据：

|组件  |说明  |
|---------|---------|
|数据访问     | 用于截获和处理 SQL 查询并生成报表的库。 此库在 Python 中实现，并支持以下 ODBC 和 DBAPI 数据源：<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|服务     | 提供 REST 终结点以针对共享数据源提供请求或查询的执行服务。 此服务旨在允许差异隐私模块进行组合，以对包含不同 delta 和 epsilon 值的请求（也称为异类请求）进行操作。 此参考实现考虑到了查询对相关数据的额外影响。 |
|计算器     | 用于检查隐私冲突、准确性和偏差的随机计算器。 计算器支持以下测试： <ul><li>隐私测试 - 确定报表是否符合差异隐私的条件。</li><li>准确性测试 - 度量报表的可靠性是否在给定 95% 置信度的上下限范围内。</li><li>实用性测试 - 确定报表的置信界限是否足够接近数据，同时仍最大限度地确保隐私。</li><li>偏差测试 - 度量重复查询报表的分布，确保它们不会失衡</li></ul> |

## <a name="next-steps"></a>后续步骤

若要了解如何使用 WhiteNoise 组件，请查看 GitHub 存储库中的 [WhiteNoise 核心包](https://github.com/opendifferentialprivacy/whitenoise-core)、[WhiteNoise 系统包](https://github.com/opendifferentialprivacy/whitenoise-system) 和 [WhiteNoise 示例](https://github.com/opendifferentialprivacy/whitenoise-samples)。