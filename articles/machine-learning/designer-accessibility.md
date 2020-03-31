---
title: 在设计器中使用辅助功能（预览版）
titleSuffix: Azure Machine Learning
description: 了解设计器中提供的键盘快捷方式和屏幕阅读器辅助功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366199"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>使用键盘使用 Azure 机器学习设计器（预览版）

了解如何使用键盘和屏幕阅读器使用 Azure 机器学习设计器。 有关 Azure 门户中所有位置工作的键盘快捷方式的列表，请参阅[Azure 门户中的键盘快捷方式](../azure-portal/azure-portal-keyboard-shortcuts.md)

此工作流已过与[讲述人和](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) [JAWS](https://www.freedomscientific.com/products/software/jaws/)一起测试，但它应与其他标准屏幕阅读器配合使用。

## <a name="navigate-the-pipeline-graph"></a>导航管道图

管道图作为嵌套列表组织。 外部列表是一个模块列表，它描述了管道图中的所有模块。 内部列表是一个连接列表，它描述特定模块的所有连接。  

1. 在模块列表中，使用箭头键切换模块。
1. 使用选项卡打开目标模块的连接列表。
1. 使用箭头键在模块的连接端口之间切换。
1. 使用"G"转到目标模块。

## <a name="edit-the-pipeline-graph"></a>编辑管道图

### <a name="add-a-module-to-the-graph"></a>向图形添加模块

1. 使用 Ctrl_F6 将焦点从画布切换到模块树。
1. 使用标准树视图控件在模块树中查找所需的模块。

### <a name="edit-a-module"></a>编辑模块

要将模块连接到其他模块，请进行：

1. 在模块列表中定位模块时，使用 Ctrl + Shift + H 打开连接帮助程序。
1. 编辑模块的连接端口。

要调整模块属性：

1. 定位模块时，使用 Ctrl + Shift + E 打开模块属性。
1. 编辑模块属性。

## <a name="navigation-shortcuts"></a>导航快捷方式

| 击键 | 描述 |
|-|-|
| Ctrl + F6 | 在画布和模块树之间切换焦点 |
| Ctrl = F1   | 聚焦模块树中的节点时打开信息卡 |
| Ctrl = 移位 = H | 当焦点位于节点上时打开连接帮助器 |
| Ctrl = 移位 = E | 焦点位于节点上时打开模块属性 |
| Ctrl = G | 如果管道运行失败，将焦点移动到第一个失败节点 |

## <a name="action-shortcuts"></a>操作快捷方式

使用以下快捷方式与访问密钥。 有关访问密钥的详细信息，请参阅https://en.wikipedia.org/wiki/Access_key。

| 击键 | 操作 |
|-|-|
| 访问键 = R | 运行 |
| 访问键 = P | 发布 |
| 访问密钥 = C | 克隆 |
| 访问键 = D | 部署 |
| 访问键 = I | 创建/更新推理管道 |
| 访问密钥 = B | 创建/更新批处理推理管道 |
| 访问键 = K | 打开"创建推理管道"下拉列表 |
| 访问键 = U | 打开"更新推理管道"下拉列表 |
| 访问键 = M | 打开更多（...） 下拉列表 |

## <a name="next-steps"></a>后续步骤

- [启用高对比度或更改主题](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [微软的辅助功能相关工具](https://www.microsoft.com/accessibility)
