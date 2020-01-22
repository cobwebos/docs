---
title: 使用设计器中的辅助功能
titleSuffix: Azure Machine Learning
description: 了解设计器中提供的键盘快捷方式和屏幕阅读器辅助功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 7d5c8047fea8e9081de08b582c537711714ea463
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314226"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>使用键盘 Azure 机器学习设计器

了解如何使用键盘和屏幕阅读器来使用 Azure 机器学习设计器。 有关 Azure 门户中的所有地方使用的键盘快捷方式的列表，请参阅[Azure 门户中的键盘快捷方式](../azure-portal/azure-portal-keyboard-shortcuts.md)

此工作流已使用[讲述人](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator)和[JAWS](https://www.freedomscientific.com/products/software/jaws/)进行了测试，但它应该与其他标准屏幕阅读器一起使用。

## <a name="navigate-the-pipeline-graph"></a>导航管道图

管道图形按列表的列表组织。 顶级模块列表包含管道中的所有模块。 模块列表中的每一项都包含一个描述其所有连接的连接列表。 

1. 在模块列表中，使用箭头键切换模块。
1. 使用 "选项卡" 打开目标模块的连接列表。
1. 使用箭头键在模块的连接端口之间切换。
1. 使用 "G" 可前往目标模块。

## <a name="edit-the-pipeline-graph"></a>编辑管道图形

### <a name="add-a-module-to-the-graph"></a>向关系图中添加模块

1. 使用 Ctrl + F6 将焦点从画布切换到模块树。
1. 使用标准 treeview 控件在模块树中查找所需的模块。

### <a name="edit-a-module"></a>编辑模块

若要将模块连接到其他模块：

1. 在模块列表中以模块为目标时，使用 Ctrl + Shift + H 打开连接助手。
1. 编辑模块的连接端口。

调整模块属性：

1. 在以模块为目标时，使用 Ctrl + Shift + E 打开模块属性。
1. 编辑模块属性。

## <a name="navigation-shortcuts"></a>导航快捷方式

| 击键 | Description |
|-|-|
| Ctrl + F6 | 在画布和模块树之间切换焦点 |
| Ctrl + F1   | 专注于模块树中的节点时打开信息卡 |
| Ctrl + Shift + H | 当焦点位于节点上时打开连接助手 |
| Ctrl + Shift + E | 当焦点位于节点上时打开模块属性 |
| Ctrl + G | 如果管道运行失败，则将焦点移到第一个失败的节点 |

## <a name="action-shortcuts"></a>操作快捷方式

使用以下快捷方式和访问密钥。 有关访问密钥的详细信息，请参阅 https://en.wikipedia.org/wiki/Access_key 。

| 击键 | 行动 |
|-|-|
| 访问键 + R | 运行 |
| 访问键 + P | 发布 |
| 访问键 + C | 克隆 |
| 访问键 + D | 部署 |
| 访问键 + I | 创建/更新推理管道 |
| 访问键 + B | 创建/更新批处理推理管道 |
| 访问键 + K | 打开 "创建推理管道" 下拉列表 |
| 访问键 + U | 打开 "更新推理管道" 下拉列表 |
| 访问键 + M | 打开 "更多（...）" 下拉列表 |

## <a name="next-steps"></a>后续步骤

- [启用高对比度或更改主题](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft 的辅助功能相关工具](https://www.microsoft.com/accessibility)
