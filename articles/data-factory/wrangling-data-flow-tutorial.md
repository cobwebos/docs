---
title: Azure 数据工厂中的整理数据流入门
description: 有关如何使用整理数据流在 Azure 数据工厂中准备数据的教程
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: f5a7f372f286a7b26a4a9916ed9df913b151e967
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628463"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>用整理数据流准备数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="create-a-wrangling-data-flow"></a>创建整理数据流

可通过两种方法在 Azure 数据工厂中创建整理数据流。 一种方法是单击加号图标，然后在 "工厂资源"**窗格中选择 "数据流"** 。

![整理](media/wrangling-data-flow/tutorial7.png)

另一种方法是在管道画布的 "活动" 窗格中。 打开 "**移动和转换**" 可折叠面板，并将**数据流**活动拖到画布上。

在这两种方法中，在打开的侧窗格中，选择 "**创建新**数据流" 并选择 "**整理**数据流"。 单击“确定”。

![整理](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>创作整理数据流

添加整理数据流的**源数据集**。 您可以选择现有数据集，也可以创建一个新数据集。 还可以选择接收器数据集。 你可以选择一个或多个源数据集，但此时只允许一个接收器。 选择接收器数据集是可选的，但至少需要一个源数据集。

> [!NOTE]
> 仅限 ADLS 第2代带分隔符的文本可用于有限预览。 

![整理](media/wrangling-data-flow/tutorial4.png)

单击 "**创建**" 以打开 "Power Query Online 混合编辑器"。

![整理](media/wrangling-data-flow/tutorial5.png)

使用无代码数据准备创作你的整理数据流。 有关可用函数的列表，请参阅[转换函数](wrangling-data-flow-functions.md)/

![整理](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>运行和监视整理数据流

若要对整理数据流执行管道调试运行，请在管道画布中单击 "**调试**"。 发布数据流后， **Trigger 立即**执行最后发布的管道的按需运行。 可以通过所有现有的 Azure 数据工厂触发器来计划整理数据流。

![整理](media/wrangling-data-flow/tutorial3.png)

中转到 "**监视**" 选项卡，直观显示触发的整理数据流活动运行的输出。

![整理](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>后续步骤

了解如何[创建映射数据流](tutorial-data-flow.md)。