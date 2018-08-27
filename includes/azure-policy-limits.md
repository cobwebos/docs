---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185270"
---
Azure Policy 的每个对象类型都有一个最大计数。 _作用域_条目是指订阅或[管理组](../articles/azure-resource-manager/management-groups-overview.md)。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 250 |
| 范围 | 计划定义 | 100 |
| 租户 | 计划定义 | 1000 |
| 范围 | 策略/计划分配数 | 100 |
| 策略定义 | parameters | 20 |
| 计划定义 | 策略 | 100 |
| 计划定义 | parameters | 100 |
| 策略/计划分配数 | 排除项 (notScopes) | 100 |
| 策略规则 | 嵌套式条件语句 | 512 |
