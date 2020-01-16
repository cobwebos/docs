---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 226079b1970508004c55c54cd3de6d2e9475aa08
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970130"
---
对资源应用标记以后，即可使用这些标记查看资源的成本。 成本分析显示最新使用情况需要一定的时间，因此可能还看不到这些成本。 成本可用以后，即可在订阅中跨资源组查看资源的成本。 用户必须具有[计费信息的订阅级别访问权限](../articles/cost-management-billing/manage/manage-billing-access.md)才能查看这些成本。

若要在门户中按标记查看成本，请先选择订阅，然后选择“成本分析”。 

![成本分析](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

然后，按标记值进行筛选并选择“应用”。 

![按标记查看成本](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

也可使用 [Azure 计费 API](../articles/cost-management-billing/manage/usage-rate-card-overview.md) 以编程方式查看成本。
