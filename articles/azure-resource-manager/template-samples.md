---
title: Azure 资源管理器模板示例
description: Azure 资源管理器模板示例，用于部署管理功能，例如角色和锁。
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390183"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>适用于管理功能的 Azure 资源管理器模板

下表包含的 Azure 资源管理器模板链接，此类模板适用于资源管理器提供的功能。

| | |
|-|-|
|**角色分配**||
| [为资源组分配角色](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| 将内置角色分配到现有资源组的用户。 |
| [为现有虚拟机分配角色](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| 将内置角色分配到现有 VM 的用户。 |
| [为多个虚拟机分配角色](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| 将内置角色分配到多个虚拟机的用户。 |
| [为 Azure 订阅分配角色](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| 为 Azure 订阅的用户分配角色。 |
|**角色定义**||
| [创建自定义角色定义](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| 在 Azure 订阅中创建新的角色定义。 |
|**资源锁**||
| [锁定资源组](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| 创建一个资源组，并将 **DoNotDelete** 锁定应用到资源组。 将参与者角色分配给用户。 |
| | |
