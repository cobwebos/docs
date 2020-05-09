---
title: Azure 预留建议是如何创建的
description: 了解如何为虚拟机创建 Azure 预留建议。
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508473"
---
# <a name="how-reservation-recommendations-are-created"></a>如何创建预预留建议

Azure 预留实例 (RI) 购买建议通过 Azure 消耗[预留建议 API](/rest/api/consumption/reservationrecommendations)、[Azure 顾问](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)和 Azure 门户中的预留购买体验提供。

以下步骤定义如何计算建议：

1. 建议引擎评估在过去 7 天、30 天和 60 天内给定范围内资源的每小时使用情况。
2. 引擎在模拟成本时可以使用预留，也可以不使用预留，具体取决于使用情况数据。
3. 针对不同数量来模拟成本，建议使用可以将节省最大化的数量。
4. 如果资源定期关闭，则模拟会找不到任何节省项目，因此不提供购买建议。

## <a name="recommendations-in-azure-advisor"></a>Azure 顾问中的建议

Azure 顾问中提供虚拟机的预留购买建议。 请注意以下几点：

- 顾问只有单一订阅范围的建议。
- 顾问中提供了使用 30 天回溯期进行计算的建议。
- 如果购买共享范围预留，则顾问预留购买建议最多可能需要 30 天才会消失。

## <a name="other-expected-api-behavior"></a>其他预期的 API 行为

- 当使用七天的回溯期时，如果 VM 关闭超过一天，则可能无法获得建议。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 预留折扣如何应用于虚拟机](../manage/understand-vm-reservation-charges.md)。
