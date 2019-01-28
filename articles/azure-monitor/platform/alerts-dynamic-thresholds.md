---
title: 在 Azure Monitor 中创建具有动态阈值的警报
description: 使用基于机器学习的动态阈值创建警报
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449004"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Azure Monitor 中具有动态阈值的指标警报（公共预览版）

具有动态阈值的指标警报检测功能利用高级机器学习 (ML) 来学习指标的历史行为，并识别可能指示存在服务问题的模式和异常。 它支持简单 UI 和大规模操作，可让用户通过 Azure 资源管理器 API 以完全自动化的方式配置警报规则。

创建警报规则后，仅当受监视指标的行为不符合预期时（根据定制的阈值来确定），才会触发该规则。

随时欢迎向 azurealertsfeedback@microsoft.com 发送反馈，我们很乐意听取你的意见。

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>使用建议的动态条件类型的原因和时机

1. **可缩放的警报** – 动态阈值警报规则一次可为数百个指标系列创建定制的阈值。 此外，它可让用户轻松基于单个指标定义警报规则。 使用 UI 或 Azure 资源管理器 API 可以减少要管理的警报规则数目。 处理指标维度或者应用到多个资源（例如所有订阅资源）时，可缩放的方法特别有用。 这相当于大幅节省了警报规则的管理和创建时间。 [详细了解如何使用模板配置具有动态阈值的指标警报](alerts-metric-create-templates.md)。

1. **智能识别指标模式** – 使用我们独特的机器学习技术可以自动检测指标模式，并不断适应指标的变化（通常包括每小时/每天/每周等季节性变化）。 不断适应指标的行为，并根据指标与模式之间的偏差发出警报可以缓解识别每个指标的“适当”阈值的压力。 动态阈值中使用的机器学习算法旨在防止出现不能提供预期模式的干扰性（低精度）阈值或宽泛性（低召回率）阈值。

1. **直观配置** – 动态阈值允许使用高级概念设置指标警报，使用户无需在指标方面具有丰富的领域知识。

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>如何配置具有动态阈值的警报规则？

可以通过 Azure Monitor 中的“指标警报”配置具有动态阈值的警报。 [详细了解如何配置指标警报](alerts-metric.md)。

## <a name="how-are-the-thresholds-calculated"></a>如何计算阈值？

动态阈值持续学习指标系列的数据，并尝试使用一组算法和方法来为此数据建模。 它会检测数据中的模式（例如每小时/每天/每周等季节性），并且能够处理干扰性指标（例如计算机 CPU 或内存），以及离散度较低的指标（例如可用性和误差率）。

如果与选择的阈值之间有偏差，则表示指标行为存在异常。

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>动态阈值中的“敏感度”设置是指什么？

警报阈值敏感度是一个高级概念，用于控制触发警报所要实现的指标行为的偏差量。
无需具备指标的领域知识（例如静态阈值）就能使用此选项。 可用选项包括：

- 高 - 阈值比较严格，并且与指标系列模式接近。 警报规则将在偏差最小时触发，因此会生成更多的警报。
- 中 – 不太严格且比较均衡的阈值，生成的警报比使用高敏感度（默认设置）时更少。
- 低 – 阈值比较宽松，与指标系列模式的偏差更大。 只会在偏差较大时触发警报规则，因此生成的警报较少。

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>动态阈值中的“运算符”设置选项有哪些？

动态阈值警报规则可以使用相同的警报规则，基于指标行为的上限和下限创建定制的阈值。
可以选择要在符合以下三种条件之一时触发的警报：

- 大于阈值上限或低于阈值下限（默认条件）
- 大于阈值上限
- 低于阈值下限。

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>动态阈值中的高级设置是指什么？

**失败时段** - 动态阈值还允许配置“触发警报之前的违规次数”，即，系统在引发警报之前，在特定时间范围内所要出现的最小偏离次数（默认时间范围是在 20 分钟内出现 4 次偏离）。 用户可以配置失败时段，并通过更改失败时段和时间范围，来选择要针对哪些指标发出警报。 此功能可以减少暂时性高峰生成的警报干扰。 例如：

若要在问题持续 20 分钟（给定时间段为 5 分钟时连续 4 次）时触发警报，请使用以下设置：

![失败时段设置为 20 分钟内持续出现问题，在给定的 5 分钟时间段内连续出现 4 次](media/alerts-dynamic-thresholds/0008.png)

若要在过去 30 分钟（时间段为 5 分钟）内有 20 分钟出现违反动态阈值的情况下触发警报，请使用以下设置：

![失败时段设置为在过去 30 分钟内有 20 分钟出现问题，时间段为 5 分钟](media/alerts-dynamic-thresholds/0009.png)

**忽略此前的数据** - 用户还可以选择性地定义一个开始日期，从此日期开始，系统应开始计算阈值。 一个典型的用例是资源过去在测试模式下运行，现在模式已提升，以便为生产工作负荷提供服务，在这种情况下，应忽略任何指标在测试阶段的行为。

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>指标中的慢速行为变更是否会触发警报？

很有可能并非如此。 动态阈值适合检测重大偏差，而不适合检测逐渐形成的问题。

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>需要使用多少数据来预览再计算阈值？

基于指标创建警报规则之前，图表中显示的阈值是根据过去 10 天的历史数据计算得到的，一旦创建警报规则，动态阈值就会获取更多可用的历史数据，并根据新数据不断学习，使自身更加准确。
