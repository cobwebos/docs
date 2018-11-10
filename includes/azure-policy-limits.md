---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964538"
---
Azure Policy 的每个对象类型都有一个最大计数。 _作用域_条目是指订阅或[管理组](../articles/governance/management-groups/overview.md)。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 250 |
| 范围 | 计划定义 | 100 |
| 租户 | 计划定义 | 1000 |
| 范围 | 策略/计划分配数 | 100 |
| 策略定义 | parameters | 20 |
| 计划定义 | 策略 | 100 |
| 计划定义 | parameters | 100 |
| 策略/计划分配数 | 排除项 (notScopes) | 250 |
| 策略规则 | 嵌套式条件语句 | 512 |
