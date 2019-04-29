---
title: Azure 数据工厂中的追加变量活动 | Microsoft Docs
description: 了解如何设置“追加变量”活动以将值添加到数据工厂管道中定义的现有数组变量
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557216"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的追加变量活动

使用“追加变量”活动将值添加到数据工厂管道中定义的现有数组变量。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 需要
-------- | ----------- | --------
名称 | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 活动类型为 AppendVariable | 是
value | 用于追加到指定变量的字符串文本或表达式对象值 | 是
variableName | 活动将修改的变量的名称，该变量必须是“Array”类型 | 是

## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [设置变量活动](control-flow-set-variable-activity.md)
