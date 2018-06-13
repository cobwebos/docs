---
title: 了解 Azure 的详细使用情况
description: 了解如何阅读并理解 Azure 订阅的详细使用情况 CSV 部分
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 0f698bff1506484e1dd4afe22f7c14e0a710937c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771132"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>了解 Microsoft Azure 详细使用费条款 

详细使用费 CSV 文件包含当前计费周期的每日和测定仪级使用费。 

若要获取详细使用情况文件，请参阅[如何获取 Azure 帐单发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)。
它以逗号分隔值 (.csv) 文件格式提供，可以在电子表格应用程序中打开它。 如果看到两个版本可用，请下载版本 2。 这是最新的文件格式。

使用费是订阅的总“月度”费用。 使用费不考虑任何信用额度或折扣。

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>详细使用情况文件的详细条款和说明

以下部分介绍版本 2 的详细使用情况文件中显示的重要条款。

### <a name="statement"></a>语句

详细使用情况 CSV 文件的顶部显示了你在这个月的计费周期期间使用的服务。 下表列出了此部份中显示的条款和说明。

| 术语 | 说明 |
| --- | --- |
|计费周期 |使用测定仪时的计费周期 |
|测定仪类别 |标识该次使用的最上层服务 |
|测定仪子类别 |定义 Azure 服务类型，该类型可能会影响费率 |
|测定仪名称 |标识已使用计量的度量单位 |
|测定仪区域 |标识某些服务的数据中心的位置，这些服务根据数据中心位置进行定价 |
|SKU |指明每个 Azure 测定仪的唯一系统标识符 |
|单位 |指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
|已耗用数量 |计费周期期间使用的计量的量 |
|附送数量 |当前计费周期中免费附送的计量的量 |
|超额数量 |显示已耗用数量和已包含数量之间的差异。 将根据此数量对你计费。 对于不附送任何数量的即用即付产品/服务，此总数与已耗用数量相同。 |
|在承诺额范围内 |显示从与你的 6 或 12 个月产品/服务关联的承诺金额中减去的测定仪费用。 按时间顺序减去测定仪费用。 |
|货币 |当前计费期间内使用的货币 |
|超额 |显示超过与你的 6 或 12 个月产品/服务关联的承诺金额的测定仪费用 |
|承诺费率 |显示基于与你的 6 或 12 个月产品/服务关联的总承诺金额的承诺费率 |
|费率 |为每个可付费单位支付的费率 |
|值 |显示将“超额数量”列与“费率”列相乘的结果。 如果已耗用数量未超过已包含数量，则此列中没有费用。 |

### <a name="daily-usage"></a>每日使用情况

该 CSV 文件的“每日使用情况”部分显示影响计费费率的使用详细信息。 下表列出了此部份中显示的条款和说明。

| 术语 | 说明 |
| --- | --- |
|使用日期 |使用测定仪时的日期 |
|测定仪类别 |标识该次使用所属的最上层服务 |
|测定仪 ID |用于计算用量费用的费用计量标识符 |
|测定仪子类别 |定义 Azure 服务类型，该类型可能会影响费率 |
|测定仪名称 |标识已使用计量的度量单位 |
|测定仪区域 |标识某些服务的数据中心的位置，这些服务根据数据中心位置进行定价 |
|单位 |指明测定仪的计价单位。 例如，GB、小时、10,000 秒。 |
|已耗用数量 |当日已耗用的测定仪量 |
|资源位置 |指明测定仪正在其中运行的数据中心 |
|已耗用的服务 |你使用的 Azure 平台服务 |
|资源组 |部署的测定仪正在其中运行的资源组。 <br/><br/>有关详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 |
|实例 ID | 测定仪的标识符。 <br/><br/> 此标识符包含你在测定仪创建时为其指定的名称。 它是资源的名称或完全限定的资源 ID。 有关详细信息，请参阅 [Azure 资源管理器 API](https://docs.microsoft.com/rest/api/resources/resources)。 |
|标记 | 分配给测定仪的标记。 使用标记对计费记录进行分组。<br/><br/>例如，可以使用标记按使用测定仪的部门分配费用。 支持发出标记的服务包括虚拟机、存储和使用 [Azure 资源管理器 API](https://docs.microsoft.com/rest/api/resources/resources) 预配的网络服务。 有关详细信息，请参阅[使用标记来组织 Azure 资源](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。 |
|其他信息 |服务特定的元数据。 例如，虚拟机的映像类型。 |
|服务信息 1 |订阅上服务所属的项目名称 |
|服务信息 2 |旧字段，用于捕获可选的服务特定元数据 |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>如何确保详细使用情况文件中的费用是正确的？
如果详细使用情况文件上具有你想要获取有关其详细信息的费用，请参阅[了解你的 Microsoft Azure 帐单](./billing-understand-your-bill.md)。

## <a name="external"></a>外部服务如何收费？
外部服务（也称为 Marketplace 订单）由独立的服务供应商提供，需要单独计费。 Azure 发票上不会记录该费用。 若要了解详细信息，请参阅[了解 Azure 外部服务收费](billing-understand-your-azure-marketplace-charges.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?)以快速解决问题。
