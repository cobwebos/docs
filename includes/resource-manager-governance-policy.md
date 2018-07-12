---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740530"
---
[Azure 策略](/azure/azure-policy/)可帮助确保订阅中的所有资源符合企业标准。 使用策略可通过将部署选项仅限于批准的这些资源类型和 SKU 来降低成本。 可为资源定义规则和操作，在部署过程中会自动强制执行这些规则。 例如，可以控制部署的资源类型。 或者，可以对资源限制已批准的位置。 某些策略拒绝操作，而某些策略设置操作的审核。

策略是对基于角色的访问控制 (RBAC) 的补充。 RBAC 关注用户访问权限，是默认拒绝和显式允许系统。 策略关注部署期间和部署后的资源属性。 它是默认允许和显式拒绝系统。

使用策略要了解两个概念 - *策略定义*和*策略分配*。 策略定义描述要强制实施的管理条件。 策略分配将策略定义放入特定作用域的操作。

![分配策略](./media/resource-manager-governance-policy/policy-concepts.png)

Azure 提供了几个内置策略定义，你可以使用而不用进行任何修改。 传递参数值以指定在作用域中允许的值。 如果内置策略定义不满足要求，可以[创建自定义策略定义](../articles/azure-policy/create-manage-policy.md)。
