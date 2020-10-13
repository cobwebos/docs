---
title: include 文件
description: include 文件
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91605782"
---
Azure Policy 的每个对象类型都有一个最大计数。 对于定义，“范围”条目是指[管理组](../articles/governance/management-groups/overview.md)或订阅。
对于分配和例外情况，“范围”条目是指[管理组](../articles/governance/management-groups/overview.md)、订阅、资源组或单个资源。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 500 |
| 范围 | 计划定义 | 200 |
| 租户 | 计划定义 | 2,500 |
| 范围 | 策略或计划分配 | 200 |
| 范围 | 豁免 | 1000 |
| 策略定义 | parameters | 20 |
| 计划定义 | 策略 | 1000 |
| 计划定义 | parameters | 100 |
| 策略或计划分配 | 排除项 (notScopes) | 400 |
| 策略规则 | 嵌套式条件语句 | 512 |
| 修正任务 | 资源 | 500 |
