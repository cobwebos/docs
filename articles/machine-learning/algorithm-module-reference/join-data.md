---
title: 联接数据：模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何使用联接的数据模块在 Azure 机器学习服务中的联接合并数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518009"
---
# <a name="join-data"></a>联接数据

本文介绍如何使用**联接数据**模块在 Azure 机器学习服务的可视界面要合并两个数据集使用数据库样式联接操作。  

## <a name="how-to-configure-join-data"></a>如何配置联接数据

若要在两个数据集上执行联接，它们应相关的键列。 此外支持使用多个列的复合键。 

1. 添加你想要将结合使用，并将数据的集**加入数据**入试验的模块。 

    也可以找到该模块中的**Data Transformation**类别下**操作**。

1. 连接到数据集**加入数据**模块。 
 
1. 选择**启动列选择器**选择键列。 请记住来选择用于左侧和右侧输入列。

    单个键：

    选择一个键列的两个输入。
    
    对于组合键：

    从左侧的输入和右输入的相同顺序选择所有键列。 **联接数据**所有键列匹配时，模块将联接表。 选中选项**允许存在重复项并保留所选内容中的列顺序**如果列的顺序不是与原始表相同。 

    ![列选择器](media/module/join-data-column-selector.png)


1. 选择**区分大小写**选项如果想要保留是否区分大小写的文本列联接。 
   
1. 使用**联接类型**下拉列表指定应如何组合数据集。  
  
    * **内部联接**:*内部联接*是最常见的联接操作。 仅当键列的值匹配时，它返回组合的行。  
  
    * **左外部联接**:一个*左外部联接*返回联接左表中的所有行的行。 如果左侧表中的行右表中没有匹配行，将返回的行包含来自右表的所有列的缺失值。 此外可以指定缺失值的替换值。  
  
    * **完全外部联接**:一个*完全外部联接*返回左侧表中的所有行 (**table1**) 和右表中 (**table2**)。  
  
         对于每个有没有匹配的行中的其他两个表中的行，结果将包括包含缺失值的行。  
  
    * **左半联接**:一个*左半部联接*左侧表中返回的值仅当键列的值匹配时。  

1. 选项**联接的表中保留右键列**:

    * 选择此选项可以查看两个输入表中的键。
    * 取消选择唯一返回结果的键列从左侧的输入。

1. 运行试验，或选择联接的数据模块和所选**运行所选**联接的执行。

1. 若要查看结果，请右键单击**加入数据** > **结果数据集** > **可视化**。

## <a name="next-steps"></a>后续步骤

请参阅[可用的模块集](module-reference.md)到 Azure 机器学习服务。 