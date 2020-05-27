---
title: 使用设计器中的辅助功能（预览版）
titleSuffix: Azure Machine Learning
description: 了解设计器中提供的键盘快捷方式和屏幕阅读器辅助功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: af676f128796c762b330d986836ea825a36cb246
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646295"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>通过键盘使用 Azure 机器学习设计器（预览版）

了解如何通过键盘和屏幕阅读器来使用 Azure 机器学习设计器。 有关 Azure 门户中的所有地方可用的键盘快捷方式的列表，请参阅 [Azure 门户中的键盘快捷方式](../azure-portal/azure-portal-keyboard-shortcuts.md)

已使用[讲述人](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) 和 [JAWS](https://www.freedomscientific.com/products/software/jaws/) 对此工作流进行了测试，但它应该能够与其他标准屏幕阅读器一起使用。

## <a name="navigate-the-pipeline-graph"></a>在管道图中导航

管道图组织为嵌套列表。 外层列表是模块列表，它描述了管道图中的所有模块。 内层列表是连接列表，它描述了特定模块的所有连接。  

1. 在模块列表中，可以使用箭头键来切换模块。
1. 可以使用 Tab 键打开目标模块的连接列表。
1. 可以使用箭头键在模块的连接端口之间切换。
1. 可以使用“G”转到目标模块。

## <a name="edit-the-pipeline-graph"></a>编辑管道图

### <a name="add-a-module-to-the-graph"></a>向图中添加模块

1. 可以使用 Ctrl+F6 将焦点从画布切换到模块树。
1. 可以使用标准树视图控件在模块树中查找所需的模块。

### <a name="edit-a-module"></a>编辑模块

若要将一个模块连接到另一个模块，请执行以下操作：

1. 当定位到模块列表中到某个模块时，使用 Ctrl + Shift + H 打开连接帮助程序。
1. 编辑模块的连接端口。

若要调整模块属性，请执行以下操作：

1. 当定位到某个模块时，使用 Ctrl + Shift + E 打开模块属性。
1. 编辑模块属性。

## <a name="navigation-shortcuts"></a>导航快捷方式

| 击键 | 说明 |
|-|-|
| Ctrl + F6 | 将焦点在画布与模块树之间切换 |
| Ctrl + F1   | 当焦点位于模块树中的某个节点上时打开信息卡 |
| Ctrl + Shift + H | 当焦点位于某个节点上时打开连接帮助程序 |
| Ctrl + Shift + E | 当焦点位于某个节点上时打开模块属性 |
| Ctrl + G | 如果管道运行失败，则将焦点移到第一个失败的节点 |

## <a name="action-shortcuts"></a>操作快捷方式

将以下快捷方式与访问键一起使用。 有关访问键的详细信息，请参阅 https://en.wikipedia.org/wiki/Access_key 。

| 击键 | 操作 |
|-|-|
| 访问键 + R | 运行 |
| 访问键 + P | 发布 |
| 访问键 + C | 克隆 |
| 访问键 + D | 部署 |
| 访问键 + I | 创建/更新推理管道 |
| 访问键 + B | 创建/更新批量推理管道 |
| 访问键 + K | 打开“创建推理管道”下拉列表 |
| 访问键 + U | 打开“更新推理管道”下拉列表 |
| 访问键 + M | 打开“更多(...)”下拉列表 |

## <a name="next-steps"></a>后续步骤

- [启用高对比度或更改主题](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft 的辅助功能相关工具](https://www.microsoft.com/accessibility)
