---
title: 可视化创作
description: 了解如何在 Azure 数据工厂中使用视觉对象创作
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: 79b89c6a6a7598dd3fbdfc5030fd59fe1aab6625
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832770"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure 数据工厂中的视觉对象创作

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂用户界面体验 (UX) 允许你以可视方式创作和部署资源为你的数据工厂而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。

目前，只有 Microsoft Edge 和 Google Chrome 支持 Azure 数据工厂用户体验。

## <a name="authoring-canvas"></a>创作画布

若要打开“创作画布”，请单击铅笔图标。 

![创作画布](media/author-visually/authoring-canvas.png)

在创作画布中，创作组成工厂的管道、活动、数据集、链接服务、数据流、触发器和集成运行时。 若要开始使用创作画布来生成管道，请参阅[使用 Copy 活动复制数据](tutorial-copy-data-portal.md)。 

默认的可视化创作体验是直接使用数据工厂服务。 还支持 Azure Repos Git 或 GitHub 集成，这样可以在生成数据工厂管道时进行源代码管理和协作。 若要详细了解这些创作体验的区别，请参阅 [Azure 数据工厂中的源代码管理](source-control.md)。

### <a name="properties-pane"></a>属性窗格

对于管道、数据集和数据流等顶层资源，可以在画布右侧的“属性”窗格中编辑顶层属性。 “属性”窗格包含“名称”、“说明”、“注释”和其他顶层属性等属性。 管道活动和数据流转换等子资源使用画布底部的面板进行编辑。 

![创作画布](media/author-visually/properties-pane.png)

默认情况下，“属性”窗格只在创建资源时打开。 若要编辑它，请单击画布右上角的“属性”窗格图标。

## <a name="expressions-and-functions"></a>表达式和函数

在 Azure 数据工厂中，可以使用表达式和函数（而不是静态值）来指定多个属性。

若要为属性值指定表达式，请选择“添加动态内容”，或在将光标移到字段时按“Alt + P”。

![添加动态内容](media/author-visually/dynamic-content-1.png)

这会打开“数据工厂表达式生成器”，在其中可以通过支持的系统变量、活动输出、函数和用户指定的变量或参数来生成表达式。 

![表达式生成器](media/author-visually/dynamic-content-2.png)

若要了解表达式语言，请参阅 [Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供反馈

选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
