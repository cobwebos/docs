---
title: "可用于 Azure 机器学习数据准备的目标/输出示例 | Microsoft Docs"
description: "本文档提供了一组可用于 Azure ML 数据准备的自定义数据目标/输出的示例"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---


# <a name="sample-of-destination-connections-python"></a>目标连接示例 (Python) 
阅读本附录前，先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)


### <a name="write-to-excel"></a>写入到 Excel 


写入到 Excel 需要一个额外的库，添加新库会记录在扩展性概述中。 `openpyxl` 是需要添加的库。

写入之前，可能需要进行其他一些更改。 某些目标格式不支持在数据准备中使用的某些数据类型。 例如，如果存在“错误”对象，这些将不会正确序列化到 Excel。 因此，在尝试写入 Excel 之前，需要执行从任何列中删除错误的“替换错误值”转换。

假设上述所有工作都已完成，以下行将数据表写入 Excel 文档中的单个表。 添加一个“写入数据流（脚本）”转换，并在表达式部分中输入以下代码：


#### <a name="on-windows"></a>在 Windows 上 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>在 macOS/OS X 上 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```

