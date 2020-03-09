---
title: Azure 订阅状态
description: 本文描述 Azure 订阅的不同状态。
keywords: Azure 订阅状态
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270234"
---
# <a name="azure-subscription-states"></a>Azure 订阅状态

本文描述 Azure 订阅可能具有的各种状态。 在 Azure 门户订阅区域中，这些状态显示为“状态”。 

| 订阅状态 | 说明 |
|-------------| ----------------|
| **活动** | Azure 订阅处于活动状态。 可以使用订阅来部署新资源及管理现有资源。|
| **已删除** | 已删除 Azure 订阅及所有基础资源/数据。 |
| **已禁用** | Azure 订阅已禁用，不再可用于创建或管理 Azure 资源。 在此状态下，将会解除分配虚拟机，释放临时 IP 地址，存储将变为只读，并禁用其他服务。 可能会由于以下原因禁用订阅：额度已过期。 达到了支出限制。 有逾期未付的帐单。 超出了信用卡限额。 或者信用卡已被明确禁用或取消。 订阅可能会保持禁用状态 1 - 90 天，具体取决于订阅类型。 之后，它将被永久删除。 有关详细信息，请参阅[重新激活已禁用的 Azure 订阅](subscription-disabled.md)。 |
| **已过期** | Azure 订阅因取消而过期。 可以重新激活已过期的订阅。 有关详细信息，请参阅[重新激活已禁用的 Azure 订阅](subscription-disabled.md)。|
| **逾期未付** | Azure 订阅有未结付款。 订阅仍处于活动状态，但如果不支付逾期费用，订阅可能会被禁用。 有关详细信息，请参阅[结算 Azure 订阅的逾期未付余款](resolve-past-due-balance.md)。 |
