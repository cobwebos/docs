---
title: include 文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854074"
---
Azure Policy 的每个对象类型都有一个最大计数。 _作用域_条目是指订阅或[管理组](../articles/governance/management-groups/overview.md)。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 500 |
| 范围 | 计划定义 | 200 |
| 租户 | 计划定义 | 2,500 |
| 范围 | 策略或计划分配 | 200 |
| 策略定义 | 参数 | 20 |
| 计划定义 | 策略 | 1000 |
| 计划定义 | 参数 | 100 |
| 策略或计划分配 | 排除项 (notScopes) | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 500 |
