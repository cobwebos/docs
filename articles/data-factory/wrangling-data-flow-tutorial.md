---
title: 开始在 Azure 数据工厂中展开数据流
description: 关于如何使用争鸣数据流在 Azure 数据工厂中准备数据的教程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682278"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>使用争鸣的数据流准备数据

## <a name="create-a-wrangling-data-flow"></a>创建争鸣的数据流

在 Azure 数据工厂中创建争鸣的数据流有两种方法。 一种方法是单击加号图标，并在工厂资源窗格中选择 **"数据流**"。

![争吵](media/wrangling-data-flow/tutorial7.png)

另一种方法位于管道画布的活动窗格中。 打开 **"移动和变换**手风琴"并将**数据流**活动拖到画布上。

在这两种方法中，在打开的侧窗格中，选择 **"创建新数据流"** 并选择 **"旋转数据流**"。 单击“确定”。

![争吵](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>编写一个争论的数据流

为争号数据流添加**源数据集**。 您可以选择现有数据集或创建新数据集。 您还可以选择接收器数据集。 您可以选择一个或多个源数据集，但此时只允许一个接收器。 选择接收器数据集是可选的，但至少需要一个源数据集。

> [!NOTE]
> 有限预览仅支持 ADLS 第 2 代分隔文本。 

![争吵](media/wrangling-data-flow/tutorial4.png)

单击"**创建**"以打开电源查询联机混搭编辑器。

![争吵](media/wrangling-data-flow/tutorial5.png)

使用无代码数据准备编写您争论的数据流。 有关可用函数的列表，请参阅[转换函数](wrangling-data-flow-functions.md)/

![争吵](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>运行和监视争鸣的数据流

要执行争鸣数据流的管道调试运行，请单击管道画布中的**调试**。 发布数据流后，"**触发器"现在**执行最后一个已发布的管道的按需运行。 可以与所有现有的 Azure 数据工厂触发器一起计划进行争用数据流。

![争吵](media/wrangling-data-flow/tutorial3.png)

转到 **"监视器"** 选项卡，以可视化触发的争鸣数据流活动运行的输出。

![争吵](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>后续步骤

了解如何[创建映射数据流](tutorial-data-flow.md)。