---
title: Azure 资源管理器模板示例
description: 适用于资源管理器中功能的 Azure 资源管理器模板示例
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: fe7804428a429ac705bdcb2932726f366a17a3b3
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854851"
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
