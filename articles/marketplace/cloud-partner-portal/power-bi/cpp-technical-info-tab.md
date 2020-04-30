---
title: Power BI 应用服务的技术信息 |Azure Marketplace
description: 配置 Power BI 应用产品/服务在 Microsoft AppSource 市场中的“技术信息”字段。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b012ac012c7de3c6428ecbe2d2b922a2a9aa0523
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141778"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 应用“技术信息”选项卡

>[!Important]
>从2020年4月13日开始，我们开始将 Power BI 应用程序的管理转移到合作伙伴中心。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[Power BI 应用创建概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)中的说明来管理迁移的产品/服务。

在 "**新建产品/服务**" 页上，使用 "**技术信息**" 选项卡提供验证新产品/服务所需的 Power BI 安装程序包 URL 和其他信息。  对于初始版本，所有 Power BI 应用都是免费的，可从 AppSource 下载。 因此，无法为此产品类型定义库存单位（Sku）。

!["技术信息" 选项卡](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>“技术信息”字段 

在 "**技术信息**" 选项卡上，完成下表中所述的字段。 字段标签末尾的星号（*）表示该字段是必填字段。

|        字段          |  说明                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **安装程序 URL\***     | Power BI 在发布应用并将其升级到生产环境时生成此 URL。  有关详细信息，请参阅[在 Power BI 中将应用发布到仪表板和报表](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。  |
|  **验证说明**  |  如果需要，请添加说明（最多3000个字符），以帮助 Microsoft 验证团队配置、连接和测试你的应用。 包括典型的配置设置、帐户、参数或可用于测试 "连接数据" 选项的其他信息。 此信息仅对验证团队可见，并仅用于验证目的。  |
| **此应用是否以 Power BI 内容包的形式创建？** | 目前，此字段仅在内部使用。 保留默认设置 "**否**"。 如果将此设置更改为 **"是"**，则可以停止发布过程。  |  
|  |  |


## <a name="next-steps"></a>后续步骤

在 "[店面详细信息](./cpp-storefront-details-tab.md)" 选项卡上，为你的应用提供营销和法律信息。

