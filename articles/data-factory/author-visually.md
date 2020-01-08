---
title: 视觉对象创作
description: 了解如何在 Azure 数据工厂中使用视觉对象创作
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440939"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure 数据工厂中的视觉对象创作

Azure 数据工厂用户界面体验 (UX) 允许你以可视方式创作和部署资源为你的数据工厂而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。

目前，Microsoft Edge 和 Google Chrome 仅支持 Azure 数据工厂 UX。

## <a name="authoring-canvas"></a>创作画布

若要打开**创作画布**，请单击铅笔图标。 

![创作画布](media/author-visually/authoring-canvas.png)

在这里，你将创作构成工厂的管道、活动、数据集、链接的服务、数据流、触发器和集成运行时。 若要开始使用创作画布构建管道，请参阅[使用复制活动复制数据](tutorial-copy-data-portal.md)。 

默认的可视化创作体验直接使用数据工厂服务。 还支持 Azure Repos Git 或 GitHub 集成，以允许对数据工厂管道进行源代码管理和协作。 若要详细了解这些创作体验之间的差异，请参阅[Azure 数据工厂中的源代码管理](source-control.md)。

## <a name="expressions-and-functions"></a>表达式和函数

可以使用表达式和函数代替静态值来指定 Azure 数据工厂中的多个属性。

若要指定属性值的表达式，请选择 "**添加动态内容**"，或者单击 " **Alt + P** "，同时将焦点放在字段上。

![添加动态内容](media/author-visually/dynamic-content-1.png)

这将打开 "**数据工厂表达式生成器**"，您可以在其中从受支持的系统变量、活动输出、函数和用户指定的变量或参数生成表达式。 

![表达式生成器](media/author-visually/dynamic-content-2.png)

有关表达式语言的信息，请参阅[Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供反馈

选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
