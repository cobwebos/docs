---
title: 查看市场套餐的状态 - Azure 市场 | Microsoft Docs
description: 使用云合作伙伴门户查看 Azure 和 AppSource 市场中套餐的状态
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729732"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>查看 Azure 市场和 AppSource 套餐的发布状态

创建套餐之后，尤其是在发布过程中，可以在云合作伙伴门户中查看该套餐的状态。  门户的[**所有套餐**](../portal-tour/cpp-all-offers-page.md)和[**审批**](../portal-tour/cpp-approvals-page.md)页中会提供总体发布状态。  对于每个套餐，应会显示以下状态指示器之一。  

|            状态              |   描述                                                           |
|            ------              |   -----------                                                           |
| **-**                          | 套餐已创建，但发布过程尚未开始。            |
| **发布正在进行**        | 套餐正在经历发布过程的各个步骤。   |
| **发布失败**             | Microsoft 在验证或评审期间发现了严重问题。 |
| **发布已取消**           | 发布者已取消套餐发布过程。  在此状态下，不会删除市场中的现有套餐。 | 
| **正在等待发布者签收** | 套餐已由 Microsoft 评审，目前正在等待发布者做最终验证。 |
| **已删除**                   | 已删除市场中以前发布的套餐。      | 
|  |  |


## <a name="publishing-status-details"></a>发布状态详细信息 

在“新建套餐”页的“状态”选项卡中，可以找到有关套餐在经历发布过程时所处的各种状态的更多详细信息。  此页将列出该套餐类型的所有发布步骤。  *请注意，编号和特定的步骤通常根据套餐类型的不同而异。*  此页还会指出 Microsoft 验证和评审步骤所提出的任何未处理问题，通常，发布者需要先对这些问题采取措施，然后发布过程才能继续。  例如，下图显示了某个新虚拟机套餐的“状态”选项卡。 

![VM 套餐的“状态”选项卡](./media/vm-offer-pub-steps1.png)

下面是某个咨询服务的示例“状态”选项卡，其中显示了潜在顾客管理设置中报告的错误。  由于咨询服务需要潜在顾客管理，必须先更正此错误，发布才能继续。

![显示了错误的咨询服务“状态”选项卡](./media/consulting-service-error.png)

最后一个 Azure 应用程序状态示例显示严重的 Microsoft 评审问题。  其中显示了包含此评审问题详细信息的 VSTS 项的热链接。  有关详细信息，请参阅 [发布 Azure 应用程序套餐](cpp-publish-offer.md)。

![显示了评审问题的 Azure 应用“状态”选项卡](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>后续步骤

若要更正尚未处理的问题或更新套餐设置，必须[更新套餐](./cpp-update-offer.md)。 
