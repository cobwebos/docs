---
title: Azure 订阅状态
description: 描述 Azure 订阅的不同状态
keywords: Azure 订阅状态
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200531"
---
# <a name="azure-subscription-states"></a>Azure 订阅状态
本文描述 Azure 订阅可能具有的各种状态。 在订阅边栏选项卡下，这些状态显示为“状态”。

| 订阅状态 | 说明 |
|-------------| ----------------|
| **活动** | Azure 订阅处于活动状态。 可以使用此订阅来部署新资源及管理现有资源。|
| **逾期未付** | Azure 订阅有未结付款。 订阅仍处于活动状态，但如果不支付逾期费用，订阅可能会被禁用。 [处理 Azure 订阅的逾期未付余款。](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **已禁用** | Azure 订阅已禁用，不再可用于创建或管理 Azure 资源。 在此状态下，将会解除分配虚拟机，释放临时 IP 地址，存储将变为只读，并禁用其他服务。 订阅被禁用的可能原因包括你的额度已过期、已达到支出限制、有帐单逾期未付、已超出信用卡限制，或者你明确禁用/取消了订阅。 根据订阅的类型和禁用原因，订阅可能会保持禁用状态 1 - 90 天，过后将永久删除。 [重新激活已禁用的 Azure 订阅。](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **已删除** | 已删除 Azure 订阅及所有基础资源/数据。 |
