---
title: Azure 订阅状态
description: 本文描述 Azure 订阅的不同状态。
keywords: Azure 订阅状态
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: andalmia
ms.openlocfilehash: 8deda3d8f584c83b61ae50c86c3ee9f43b247ae2
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735398"
---
# <a name="azure-subscription-states"></a>Azure 订阅状态

本文描述 Azure 订阅可能具有的各种状态。 在 Azure 门户订阅区域中，这些状态显示为“状态”。

| 订阅状态 | 说明 |
|-------------| ----------------|
| **活动** | Azure 订阅处于活动状态。 可以使用订阅来部署新资源及管理现有资源。<br><br>所有操作（PUT、PATCH、DELETE、POST、GET）都可用于[为订阅注册](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)的资源提供程序。 |
| **已删除** | 已删除 Azure 订阅及所有基础资源/数据。<br><br>无可用操作。 |
| **已禁用** | Azure 订阅已禁用，不再可用于创建或管理 Azure 资源。 在此状态下，将会解除分配虚拟机，释放临时 IP 地址，存储将变为只读，并禁用其他服务。 可能会由于以下原因禁用订阅：额度已过期。 达到了支出限制。 有逾期未付的帐单。 超出了信用卡限额。 或者信用卡已被明确禁用或取消。 订阅可能会保持禁用状态 1 - 90 天，具体取决于订阅类型。 之后，它将被永久删除。 有关详细信息，请参阅[重新激活已禁用的 Azure 订阅](subscription-disabled.md)。<br><br>已禁用用于创建或更新资源（PUT、PATCH）的操作。 还禁用了执行任务 (POST) 的操作。 可检索或删除资源（GET、DELETE）。 你的资源仍然可供使用。 |
| **已过期** | Azure 订阅因取消而过期。 可以重新激活已过期的订阅。 有关详细信息，请参阅[重新激活已禁用的 Azure 订阅](subscription-disabled.md)。<br><br>已禁用用于创建或更新资源（PUT、PATCH）的操作。 还禁用了执行任务 (POST) 的操作。 可检索或删除资源（GET、DELETE）。|
| **逾期未付** | Azure 订阅有未结付款。 订阅仍处于活动状态，但如果不支付逾期费用，订阅可能会被禁用。 有关详细信息，请参阅[结算 Azure 订阅的逾期未付余款](resolve-past-due-balance.md)。<br><br>所有操作都可用。 |
