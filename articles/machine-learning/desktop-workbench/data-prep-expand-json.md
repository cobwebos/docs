---
title: "使用 Azure Machine Learning Workbench 的“展开 JSON”转换"
description: "有关“展开 JSON”转换的参考文档"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="expand-json-transformation"></a>“展开 JSON”转换
通过“展开 JSON” 转换，用户可以将包含有效 JSON 文本的现有列展开为多列。

## <a name="how-to-perform-this-transformation"></a>如何执行此转换

若要执行此转换，请执行以下步骤：
1. 选择包含 JSON 文本的源列。
2. 从“转换”菜单中选择“展开 JSON”。 或者，右键单击源列的标头，然后从上下文菜单中选择“展开 JSON”。 
3. 单击“确定”。 
 
在源列旁边添加新列。 这些列包含 JSON 文本下一级层次结构中的属性。 属性键（如果存在）用于创建相应列的名称。 嵌套的属性将保留为 JSON 文本，用户可根据需要以迭代方式扩展或放弃。

## <a name="examples"></a>示例

源列 *Customer* 展开为两列：*Customer.Name* 和 *Customer.Phone*。

| Customer                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

