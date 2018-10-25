---
title: Azure 数据工厂中的设置变量活动 | Microsoft Docs
description: 了解如何使用“设置变量”活动来设置数据工厂管道中定义的现有变量的值
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
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076413"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure 数据工厂中的设置变量活动

使用“设置变量”活动可设置数据工厂管道中定义的 String、Bool 或 Array 类型的现有变量的值。

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 必选
-------- | ----------- | --------
名称 | 管道中活动的名称 | 是
description | 描述活动用途的文本 | 否
type | 活动类型为 SetVariable | 是
值 | 用于设置指定变量的字符串文本或表达式对象值 | 是
variableName | 此活动将设置的变量的名称 | 是


## <a name="next-steps"></a>后续步骤
了解数据工厂支持的相关控制流活动： 

- [追加变量活动](control-flow-append-variable-activity.md)
