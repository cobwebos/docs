---
title: Azure 数据工厂中的追加变量活动 | Microsoft Docs
description: 了解如何设置“追加变量”活动以将值添加到数据工厂管道中定义的现有数组变量
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 03652ce20d82565d5714cdc43a01a9e7c3074f6a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48903724"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的追加变量活动

使用“追加变量”活动将值添加到数据工厂管道中定义的现有数组变量。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 必选
-------- | ----------- | --------
名称 | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 活动类型为 AppendVariable | 是
值 | 用于追加到指定变量的字符串文本或表达式对象值 | 是
variableName | 活动将修改的变量的名称，该变量必须是“Array”类型 | 是

## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [设置变量活动](control-flow-set-variable-activity.md)
