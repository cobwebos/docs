---
title: "适用于 Azure 机器学习数据准备的受支持检查器 | Microsoft Docs"
description: "本文档提供了适用于 Azure ML 数据准备的检查器的完整列表"
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
ms.date: 09/11/2017
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="supported-inspectors-for-this-preview"></a>此预览版支持的检查器
本文档概述了可用于此预览版的一组检查器。

## <a name="the-halo-effect"></a>Halo 效果 
某些检查器支持 Halo 效果，该效果使用两种不同的颜色立即直观地显示转换所带来的更改。 灰色表示最新转换之前的以前值，蓝色表示当前值。 可以在选项中启用/禁用此效果。

## <a name="graphical-filtering"></a>图形筛选 
某些检查器支持通过将其用作编辑器来进行数据筛选。 执行此操作涉及选择图形元素，然后使用检查器窗口右上角的工具栏来筛选所选值。 

## <a name="column-statistics"></a>列统计信息
对于数值列，该检查器提供有关列的各种不同统计信息。 统计信息包括；
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
计算和显示单个数值列的直方图。 使用 Scott 规则计算默认存储桶数，可通过选项重写规则。
此检查器支持 Halo 效果。


### <a name="options"></a>选项
- 最小存储桶数（即使在选中默认存储桶时也适用）
- 默认存储桶数（Scott 规则） 
- 显示 Halo
- 内核密度绘图覆盖（高斯内核） 


### <a name="actions"></a>操作
此检查器支持通过存储桶进行筛选、单选或多选存储桶以及按之前所述应用筛选器。

## <a name="value-counts"></a>值计数
此检查器显示当前选择列的值的频率表。 默认显示前 6 位，可以将此限制更改为任意数或从底部而非顶部计数。 此检查器支持 Halo 效果。

### <a name="options"></a>选项 
- 上限值​​数
- 降序
- 包含 NULL/错误值
- 显示 Halo


### <a name="actions"></a>操作 
此检查器支持通过横栏进行筛选、单选或多选横栏以及按前面所述应用筛选器。

## <a name="box-plot"></a>盒须图 
数值列的盒须图

### <a name="options"></a>选项 
- 按列分组

## <a name="scatter-plot"></a>散点图
两个数值列的散点图，出于性能原因降低了数据采样率，可以在选项中重写样本大小。

### <a name="options"></a>选项  
- X 轴列
- Y 轴列
- 样本大小
- 按列分组


## <a name="time-series"></a>时序
X 轴上具有时间感知的折线图

### <a name="options"></a>选项
- 日期列
- 数值列
- 样本大小


### <a name="actions"></a>操作
此检查器支持通过单击和拖动选择方法进行筛选，以在图形上选择一个范围。 完成选择后，按前面所述应用筛选器。


## <a name="map"></a>映射 
绘制了点的图假定已指定纬度和经度。 必须先选择纬度。

### <a name="options"></a>选项
- 纬度列
- 经度列
- 启用群集
- 按列分组


### <a name="actions"></a>操作
此检查器支持通过在图上选择点来进行筛选。 按 Ctrl 键，然后单击并使用鼠标拖动以在点周围形成矩形。 然后按前面所述应用筛选器。
通过按映射左侧的 **E**，可以快速调整图的大小以显示所有可能的点和不再显示更多的点。
