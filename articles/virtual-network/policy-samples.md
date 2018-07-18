---
title: 策略模板示例 | Microsoft Docs
description: 适用于虚拟网络的 Azure 策略模板示例。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779522"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>适用于虚拟网络的 Azure 策略示例模板

下表包含示例 [Azure 策略](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)模板的链接。 示例位于 [Azure 策略示例存储库](https://github.com/Azure/azure-policy)中。

| | |
|---|---|
|**网络**||
| [每个 NIC 上的 NSG X](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求将特定网络安全组用于所有虚拟网络接口。 指定要使用的网络安全组的 ID。 |
| [每个子网上的 NSG X](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求将特定网络安全组用于所有子网。 指定要使用的网络安全组的 ID。 |
| [无路由表](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |禁止通过路由表部署虚拟网络。 |
| [对 VM 网络接口使用已批准的子网](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求网络接口使用已批准的子网。 指定已批准的子网的 ID。 |
| [对 VM 网络接口使用已批准的 vNet](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求网络接口使用已批准的虚拟网络。 由你指定已批准的虚拟网络的 ID。 |
|**监视**||
| [审核诊断设置](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 如果未对指定资源类型启用诊断设置，则进行审核。 指定一个资源类型的数组以检查是否已启用诊断设置。 |
|**名称和文本约定**||
| [允许多种名称模式](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 允许资源使用多种名称模式中的一种。 |
| [需要类似模式](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 确保资源名称符合模式的 *like* 条件。 |
| [需要匹配模式](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 确保资源名称与指定的命名模式匹配。 |
| [需要标记匹配模式](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 确保标记值与文本模式匹配。 |
|**标记**||
| [计费标记策略计划](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求成本中心和产品名称有指定的标记值。 使用内置策略来应用并强制执行所需标记。 指定所需的标记值。  |
| [在资源组强制执行标记及其值](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求资源组有标记和值。 由你指定标记名称和值。  |
| [强制实施标记和值](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 需要指定的标记名称和值。 指定要强制实施的标记名称和值。  |
| [应用标记及其默认值](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 如果未提供标记，追加指定的标记名称和值。 由你指定要应用的标记名称和值。  |
|**常规**||
| [允许的位置](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 要求所有资源都部署到批准的位置。 由你指定一个已批准的位置的数组。  |
| [允许的资源类型](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 确保仅部署已批准的资源类型。 指定一个允许的资源类型的数组。  |
| [不允许的资源类型](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 禁止部署指定的资源类型。 指定一个阻止使用的资源类型的数组。  |