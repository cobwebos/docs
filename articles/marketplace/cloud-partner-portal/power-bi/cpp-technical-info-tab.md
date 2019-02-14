---
title: Power BI 应用产品/服务的技术信息 - Azure 市场 | Microsoft Docs
description: 配置 Power BI 应用产品/服务在 Microsoft AppSource 市场中的“技术信息”字段。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744428"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 应用“技术信息”选项卡

“新产品/服务”页面的“技术信息”选项卡是提供 Power BI 安装程序包 URL 和新产品/服务验证所需的任何其他信息的位置。  对于初始版本，所有 Power BI 应用都是免费的，可从 AppSource 下载，无额外费用。 因此，无法为此产品/服务类型定义任何库存单位 (SKU)。

![“技术信息”选项卡](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>“技术信息”字段 

在“技术信息”选项卡上，必须提供以下字段。  如果字段标签上追加​​有星号 (*)，表示此为必填字段。

|        字段          |  说明                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **安装程序 URL**     | 在发布应用并将它提升到生产环境时，Power BI 生成的地址。  有关如何生成该 URL 的详细信息，请参阅[在 Power BI 中发布服务应用](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。  |
|  **验证说明**  |  Microsoft 验证团队用于帮助配置、连接和测试应用的可选文本说明（最多 3000 个字符），包括：典型配置设置、可以用于测试“连接数据”选项的测试帐户或参数等。此信息仅对验证团队可见，并且仅用于验证。  |
| **此应用是否以 Power BI 内容包的形式创建？** | 当前，这是内部使用的字段。 请将值保留设置为其默认值 `No`；否则，将此字段更改为 `Yes` 可能会妨碍发布。  |  
|  |  |


## <a name="next-steps"></a>后续步骤

在后面的[店面详细信息](./cpp-storefront-details-tab.md)选项卡中，会提供应用的营销和法律信息。

