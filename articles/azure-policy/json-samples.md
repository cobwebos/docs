---
title: "策略模板示例 | Microsoft Docs"
description: "用于 Azure 策略的 JSON 示例"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 01/17/2018
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 783a6bd8c665f1e08d94d2712a581955be8501e9
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="templates-for-azure-policy"></a>Azure 策略模板

下表包含 Azure 策略的 json 模板链接。 这些示例位于 [Azure 策略示例存储库](https://github.com/Azure/azure-policy)。

| | |
|---|---|
|**计算**||
| [已批准的 VM 映像](scripts/allowed-custom-images.md) | 要求在环境中仅部署已批准的自定义映像。 指定已批准的映像 ID 的数组。 |
| [在 VM 未使用托管磁盘时审核](scripts/create-vm-managed-disk.md) | 如果创建了未使用托管磁盘的虚拟机，则进行审核。|
| [如果扩展不存在，则进行审核](scripts/audit-ext-not-exist.md) | 如果扩展不是通过虚拟机部署的，则进行审核。 指定扩展发布者和类型以检查是否已部署扩展。 |
| [允许自定义资源组中的 VM 映像](scripts/allow-custom-vm-image.md) |  要求自定义映像来自已批准的资源组。 指定已批准的资源组的名称。 |
| [拒绝混合使用权益](scripts/deny-hybrid-use.md) | 禁止使用 Azure 混合使用权益 (AHUB)。 想禁止使用本地许可证时使用。 |
| [不允许使用 VM 扩展](scripts/not-allowed-vm-ext.md) | 禁止使用指定的扩展。 指定一个数组，其中包含被禁止的扩展类型。 |
| [只允许特定 VM 平台映像](scripts/allow-certain-vm-image.md) | 要求虚拟机使用 UbuntuServer 的特定版本。 |
| [使用托管磁盘创建 VM](scripts/use-managed-disk-vm.md) | 要求虚拟机使用托管磁盘。|
|**监视**||
| [审核诊断设置](scripts/audit-diag-setting.md) | 如果未对指定资源类型启用诊断设置，则进行审核。 指定一个资源类型的数组以检查是否已启用诊断设置。 |
|**名称和文本约定**||
| [允许多种名称模式](scripts/allow-multiple-name-patterns.md) | 允许资源使用多种名称模式中的一种。 |
| [需要类似模式](scripts/enforce-like-pattern.md) | 确保资源名称符合模式的类似条件。 |
| [需要匹配模式](scripts/enforce-match-pattern.md) | 确保资源名称与命名模式匹配。 |
| [需要标记匹配模式](scripts/enforce-tag-match-pattern.md) | 确保标记值与文本模式匹配。 |
|**网络**||
| [允许的应用程序网关 SKU](scripts/allowed-app-gate-sku.md) | 要求应用程序网关使用已批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [如果未对区域启用网络观察程序，则进行审核](scripts/net-watch-not-enabled.md) | 如果未对指定区域启用网络观察程序，则进行审核。 指定区域的名称以检查是否启用了网络观察程序。 |
| [每个 NIC 上的 NSG X](scripts/nsg-on-nic.md) | 要求将特定网络安全组用于所有虚拟网络接口。 指定要使用的网络安全组的 ID。 |
| [每个子网上的 NSG X](scripts/nsg-on-subnet.md) | 要求将特定网络安全组用于所有子网。 指定要使用的网络安全组的 ID。 |
| [允许的 Express Route 带宽](scripts/allowed-er-band.md) | 要求 Express Route 使用一组指定的带宽。 指定可为 Express Route 指定的 SKU 数组。 |
| [允许的 Express Route 对等位置](scripts/allowed-peering-er.md) | 要求 Express Route 使用指定的对等位置。 指定一个允许的对等位置的数组。 |
| [允许的 Express Route SKU](scripts/allowed-er-skus.md) | 要求 Express Route 使用已批准的 SKU。 指定允许的 SKU 的数组。 |
| [允许的负载均衡器 SKU](scripts/allowed-lb-skus.md) | 要求负载均衡器使用已批准的 SKU。 指定允许的 SKU 的数组。 |
| [无网络与 ER 网络对等](scripts/no-peering-er-net.md) | 禁止将网络对等关联到指定资源组中的网络。 用于防止与中心托管的网络基础结构连接。 指定要防止关联的资源组的名称。 |
| [无用户定义的路由表](scripts/no-user-def-route-table.md)  |禁止通过用户定义的路由表部署虚拟网络。 |
| [允许的虚拟网络网关 SKU](scripts/no-user-def-route-table.md) | 要求虚拟网络网关使用已批准的 SKU 和网关类型。 指定一个已批准的 SKU 的数组和已批准的网关类型的数组。 |
| [对 VM 网络接口使用已批准的子网](scripts/use-approved-subnet-vm-nics.md) | 要求网络接口使用已批准的子网。 指定已批准的子网的 ID。 |
| [对 VM 网络接口使用已批准的 vNet](scripts/use-approved-vnet-vm-nics.md) | 要求网络接口使用已批准的虚拟网络。 由你指定已批准的虚拟网络的 ID。 |
|**标记**||
| [计费标记策略计划](scripts/billing-tags-policy-init.md) | 要求成本中心和产品名称有指定的标记值。 使用内置策略来应用并强制执行所需标记。 指定所需的标记值。  |
| [在资源组强制执行标记及其值](scripts/enforce-tag-rg.md) | 要求资源组有标记和值。 由你指定标记名称和值。  |
|**SQL**||
| [审核 SQL DB 级别审核设置](scripts/audit-sql-db-audit-setting.md) | 如果 SQL 数据库审核设置不匹配指定设置，则审核这些设置。 指定用于指示应启用或禁用审核设置的值。  |
| [审核透明数据加密状态](scripts/audit-trans-data-enc-status.md) | 如果 SQL 数据库透明数据加密未启用，则审核该加密。  |
| [审核 DB 级别的威胁检测设置](scripts/audit-db-threat-det-setting.md) | 如果 SQL 数据库安全警报策略未设置成指定状态，则审核这些策略。 指定一个值，该值指示威胁检测是启用还是禁用状态。  |
| [审核 SQL Server 级别审核设置](scripts/audit-sql-ser-leve-audit-setting.md) | 如果 SQL 服务器审核设置不匹配指定的设置，则审核这些设置。 指定用于指示应启用或禁用审核设置的值。 |
| [审核服务器级别的威胁检测设置](scripts/audit-sql-ser-threat-det-setting.md) | 如果 SQL 数据库安全警报策略未设置成指定状态，则审核这些策略。 指定一个值，该值指示威胁检测是启用还是禁用状态。  |
| [无 Azure Active Directory 管理员时审核](scripts/audit-no-aad-admin.md) | SQL 服务器未分配有任何 Azure Active Directory 管理员时进行审核。 |
| [允许的 SQL DB SKU](scripts/allowed-sql-db-skus.md) | 要求 SQL 数据库使用已批准的 SKU。 指定一个允许的 SKU ID 的数组或允许的 SKU 名称的数组。 |
|**存储**||
| [允许的存储帐户和虚拟网络的 SKU](scripts/allowed-skus-storage.md) | 要求存储帐户和虚拟机使用已批准的 SKU。 使用内置策略以确保使用已批准的 SKU。 指定已批准的虚拟机 SKU 数组和已批准的存储帐户 SKU 数组。 |
| [确保 https 流量仅限于存储帐户](scripts/ensure-https-stor-acct.md) | 要求存储帐户使用 HTTPS 流量。  |
| [拒绝存储帐户的冷访问层](scripts/deny-cool-access-tiering.md) | 禁止为 blob 存储帐户使用冷访问层。  |
| [确保存储文件加密](scripts/ensure-store-file-enc.md) | 要求对存储帐户启用文件加密。  |
|**内置策略**||
| [允许的位置](scripts/allowed-locs.md) | 要求所有资源都部署到批准的位置。 由你指定一个已批准的位置的数组。  |
| [允许的资源类型](scripts/allowed-res-types.md) | 确保仅部署已批准的资源类型。 指定一个允许的资源类型的数组。  |
| [允许的存储帐户 SKU](scripts/allowed-stor-acct-skus.md) | 要求存储帐户使用批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [应用标记及其默认值](scripts/apply-tag-def-val.md) | 如果未提供标记，追加指定的标记名称和值。 由你指定要应用的标记名称和值。  |
| [强制实施标记和值](scripts/enforce-tag-val.md) | 需要指定的标记名称和值。 指定要强制实施的标记名称和值。  |
| [不允许的资源类型](scripts/not-allowed-res-type.md) | 禁止部署指定的资源类型。 指定一个阻止使用的资源类型的数组。  |
| [需要 SQL Server 版本 12.0](scripts/req-sql-12.md) | 要求 SQL 服务器使用版本 12.0。  |
| [需要存储帐户加密](scripts/req-store-acct-enc.md) | 要求存储帐户使用 blob 加密。  |
