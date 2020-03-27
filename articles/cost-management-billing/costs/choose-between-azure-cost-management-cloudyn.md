---
title: 在 Azure 成本管理与 Cloudyn 之间进行选择
description: 本文帮助你确定是 Azure 成本管理还是 Cloudyn 最适合你的成本管理需求。
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 9b17cdfbae7fccae146f01654fb755f23f00563c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80083204"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>在 Azure 成本管理与 Cloudyn 之间进行选择

Cloudyn 将在 2020 年底弃用。 在可能的情况下，我们会将现有的 Cloudyn 功能直接集成到 Azure 门户中。 但 CSP 客户除外，新客户暂时都无法登记。 对现有产品的支持将会保持到它被完全弃用为止。

Microsoft 收购了 Cloudyn，并将其成本管理功能从 Cloudyn 门户本机迁移到 Azure。 若要使用新功能，请登录到 Azure 门户并导航到 Azure 服务列表中的[成本管理和计费](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)。 与 Cloudyn 相比，本机体验提高了性能并减少了大约八小时的数据延迟。

将企业协议、即用即付和 MSDN 产品/服务类别的主要功能迁移到 Azure 成本管理的过程已完成。 CSP 订阅正在迁移到 Azure 成本管理。

如果有一个产品/服务类别尚未迁移，应继续使用 Cloudyn 门户。 其他所有人都可以使用 Azure 成本管理。

## <a name="recommended-services-by-offer"></a>按套餐建议的服务

| Microsoft Azure 套餐 | 建议的成本管理服务 |
| --- | --- |
| Azure 企业协议 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft 客户协议 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| 合作伙伴支持的 Microsoft 客户协议 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>可用的成本管理功能

Cloudyn 中提供了以下某些功能，但所有这些功能现在都可在 Azure 成本管理中使用。

- API
- Azure 成本优化建议，包括但不限于：
    - Azure 实例适当调整大小和关闭建议
    - Azure 预留建议
- 预算
- 成本分析
- 将数据导出到 Azure 存储帐户
- 更低的延迟
- Power BI 模板应用
- 资源标记支持
- 对 AWS 的跨云成本分析支持

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 成本管理](../cost-management-billing-overview.md)。