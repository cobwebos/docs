---
title: "Azure 基础结构命名准则 - Windows | Microsoft Docs"
description: "了解在 Azure 基础结构服务中进行命名的关键设计和实施准则。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 660765fa-4d42-49cb-a9c6-8c596d26d221
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 87a3d4fc849cee5d9abed1e4c32a9da583ea9516
ms.openlocfilehash: 866265bfa2eb7bcfebd8c73e14d3344a150aaf09


---
# <a name="azure-infrastructure-naming-guidelines"></a>Azure 基础结构命名准则
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文着重介绍如何对各项 Azure 资源实行命名约定，以便在环境中生成一组易于识别的逻辑资源。

## <a name="implementation-guidelines-for-naming-conventions"></a>命名约定的实施准则
决策：

* 你的 Azure 资源的命名约定是什么？

任务：

* 定义要在所有资源中使用以保持一致的词缀。
* 定义给定其全局唯一的要求的存储帐户名称。
* 将要使用的命名约定记录到文件中并将其分发给所有相关方，确保各部署间一致。

## <a name="naming-conventions"></a>命名约定
在 Azure 中创建任何项目之前，你应该已确定合适的命名约定。 命名约定可确保所有资源都具有可预测的名称，这有助于减轻与这些资源的管理相关联的管理负担。

可以选择遵循一组为整个组织或特定 Azure 订阅或帐户定义的特定命名约定。 虽然组织内的个人在使用 Azure 资源时，在团队需要在 Azure 上处理项目时很容易建立隐式规则，但该模型可伸缩性差。

应事先就命名约定集达成一致意见。 关于超越这几组规则的命名约定，有一些注意事项。

## <a name="affixes"></a>词缀
要定义命名约定时，应决定词缀位于以下哪一个位置：

* 名称的开头（前缀）
* 名称的末尾（后缀）

例如，使用 `rg` 词缀的资源组可能具有以下两个名称：

* Rg-WebApp（前缀）
* WebApp-Rg（后缀）

词缀可以引用描述特定资源的不同方面。 下表显示了通常使用的一些示例。

| 方面 | 示例 | 说明 |
|:--- |:--- |:--- |
| 环境 |dev、stg、prod |根据每个环境的用途和名称。 |
| 位置 |usw（美国西部）、使用（美国东部 2） |根据数据中心的区域或组织的区域。 |
| Azure 组件、服务或产品 |Rg 用于资源组，VNet 用于虚拟网络 |根据资源提供支持的产品。 |
| 角色 |sql、ora、sp、iis |根据虚拟机的角色。 |
| 实例 |01、02、03 等。 |适用于具有多个实例的资源。 例如，云服务中经过负载平衡的 Web 服务器。 |

建立命名约定时，请确保这些命名约定明确说明要对每种类型的资源使用哪些词缀，以及在哪个位置使用（前缀还是后缀）。

## <a name="dates"></a>日期
通常情况下，从资源名称确定创建日期很重要。 我们建议使用 YYYYMMDD 日期格式。 此格式可确保不仅会记录完整的日期，而且名称只是日期不同的两个资源也会同时按字母和按时间顺序进行排序。

## <a name="naming-resources"></a>命名资源
在命名约定中定义每种类型的资源，其中应包含定义如何为创建的每个资源分配名称的规则。 这些规则应适用于所有类型的资源，例如：

* 订阅
* 帐户
* 存储帐户
* 虚拟网络
* 子网
* 可用性集
* 资源组
* 虚拟机
* 终结点
* 网络安全组
* 角色

为了确保名称可以提供足够的信息来确定它所引用的资源，你应该使用描述性名称。

## <a name="computer-names"></a>计算机名称
创建虚拟机 (VM) 时，Microsoft Azure 要求 VM 名称最多由 15 个字符组成，该名称将在资源名称中使用。 Azure 将对该 VM 中安装的操作系统使用同一名称。 但是，这些名称可能并非始终相同。

如果使用已包含操作系统的 .vhd 映像文件创建 VM，Azure 中的 VM 名称可能不同于 VM 的操作系统计算机名称。 这种情况可能会增加 VM 管理难度，因此不建议使用这种方法。 分配给 Azure VM 资源的名称可以与分配给该 VM 的操作系统的计算机名称相同。

建议 Azure VM 名称应该与基础操作系统计算机名称相同。

## <a name="storage-account-names"></a>存储帐户名称
存储帐户具有适用于其名称的特殊规则。 你只能使用小写字母和数字。 有关详细信息，请参阅[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。 此外，存储帐户名称与 core.windows.net 组合在一起应该是一个全局有效的唯一 DNS 名称。 例如，如果存储帐户名为 mystorageaccount，则下面生成的 DNS 名称应该是唯一的：

* mystorageaccount.blob.core.windows.net
* mystorageaccount.table.core.windows.net
* mystorageaccount.queue.core.windows.net

## <a name="next-steps"></a>后续步骤
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Jan17_HO4-->


