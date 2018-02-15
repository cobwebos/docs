---
title: "适用于 Azure 机器学习数据准备的受支持检查器 | Microsoft Docs"
description: "本文档提供适用于 Azure 机器学习数据准备的检查器的完整列表"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 1046f08d4e3b1111f8f82551b71252856174a308
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>适用于 Azure 机器学习数据准备预览版的受支持检查器
本文档概述了可用于此预览版的一组检查器。

## <a name="the-halo-effect"></a>光圈效果 
某些检查器支持 halo 效果。 该效果使用两种不同的颜色立即直观地显示转换所带来的更改。 灰色表示最新转换之前的值，蓝色表示当前值。 可以在选项中启用和禁用此效果。

## <a name="graphical-filtering"></a>图形筛选 
某些检查器支持通过将其用作编辑器来进行数据筛选。 将检查器用作编辑器涉及选择图形元素，然后使用检查器窗口右上方的工具栏来筛选所选值。 

## <a name="column-statistics"></a>列统计信息
对于数值列，该检查器提供有关列的各种不同统计信息。 统计信息包括以下度量值： 
- 最小值
- 下四分位数
- 中值
- 上四分位数
- 最大值
- 平均值
- 标准偏差


### <a name="options"></a>选项 
- 无

## <a name="histogram"></a>直方图 
计算和显示单个数值列的直方图。 使用 Scott 规则计算默认存储桶数。 但是，可通过选项重写规则。

此检查器支持 halo 效果。


### <a name="options"></a>选项
- 最小存储桶数（即使在选中默认存储桶时也适用）
- 默认存储桶数（Scott 规则） 
- 显示 Halo
- 内核密度绘图覆盖（高斯内核） 
- 使用对数刻度


### <a name="actions"></a>操作
此检查器支持通过存储桶进行筛选，这可能包括单选或多选存储桶。 按前面所述应用筛选器。

## <a name="value-counts"></a>值计数
此检查器显示当前选择列的值的频率表。 默认显示前六个值的数据。 但是，可将此限制更改为任意数字。 还可将显示内容设置为从底部而非顶部计数。 此检查器支持 halo 效果。

### <a name="options"></a>选项 
- 上限值​​数
- 降序
- 包含 NULL/错误值
- 显示 Halo
- 使用对数刻度


### <a name="actions"></a>操作 
此检查器支持通过条形进行筛选，这可能包括单选或多选条形。 按前面所述应用筛选器。

## <a name="box-plot"></a>盒须图 
数值列的盒须图。

### <a name="options"></a>选项 
- 按列分组

## <a name="scatter-plot"></a>散点图
两个数值列的散点图。 出于性能原因降低了数据采样率。 可以在选项中重写样本大小。

### <a name="options"></a>选项  
- X 轴列
- Y 轴列
- 样本大小
- 按列分组


## <a name="time-series"></a>时序
X 轴上具有时间感知的折线图。

### <a name="options"></a>选项
- 日期列
- 数值列
- 样本大小


### <a name="actions"></a>操作
此检查器支持通过单击和拖动选择方法进行筛选，以在图形上选择一个范围。 完成选择后，按前面所述应用筛选器。


## <a name="map"></a>映射 
绘制了点的地图假定已指定纬度和经度。 必须先选择纬度。

### <a name="options"></a>选项
- 纬度列
- 经度列
- 启用群集
- 按列分组


### <a name="actions"></a>操作
此检查器支持通过在图上选择点来进行筛选。 按 **Ctrl** 键，然后单击并使用鼠标拖动以在点周围形成矩形。 然后按前面所述应用筛选器。

通过按地图左侧的 **E**，可以快速调整地图的大小以便仅显示可能的点。


## <a name="pattern-frequency"></a>模式频率 

此检查器在所选字符串列中显示模式列表。 模式通过类似语法的正则表达式来表示。 将鼠标悬停在模式上，会显示由该模式表示的值的示例。 此外，以百分比表示的近似覆盖率和模式也会一同显示。

![模式检查器的图像](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>选项
- 上限值​​数
- 降序
- 显示 Halo

### <a name="actions"></a>操作
此检查器支持基于显示模式进行筛选。 按 Ctrl，然后选择模式检查器中填充的栏。 然后按前面所述应用筛选器。 由于用户操作，添加了高级筛选器步骤。 可通过调用高级筛选器步骤的编辑选项来查看和修改生成的 Python 代码。
