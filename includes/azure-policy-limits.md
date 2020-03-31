---
title: include 文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "80272945"
---
Azure Policy 的每个对象类型都有一个最大计数。 _作用域_条目是指订阅或[管理组](../articles/governance/management-groups/overview.md)。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 500 |
| 范围 | 计划定义 | 100 |
| 租户 | 计划定义 | 1,000 |
| 范围 | 策略或计划分配 | 100 |
| 策略定义 | 参数 | 20 |
| 计划定义 | 策略 | 100 |
| 计划定义 | 参数 | 100 |
| 策略或计划分配 | 排除项 (notScopes) | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 1000 |
