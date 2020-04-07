---
title: 电源 BI 应用产品/服务提供设置 |Azure 应用商店
description: 为 Microsoft AppSource 市场配置 Power BI 应用产品/服务的产品/服务设置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 637217d718e6e91a3fa29bd38d8215d0a1324825
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745428"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 应用套餐设置选项卡

>[!Important]
>从 2020 年 3 月 30 日开始，我们将开始将 Power BI 应用优惠的管理转移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[Power BI 应用创建概述](https://aka.ms/AzureCreatePBIServiceApp)中的说明进行操作，以管理迁移的优惠。

当您为服务应用打开 **"新产品/服务**"页面时，首先可以看到"**产品/服务设置"** 选项卡。您可以在此选项卡上提供主要标识符和产品/服务的名称。星号 （*） 表示必填字段。

![“产品/服务设置”选项卡](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>产品/服务设置字段 

在"**优惠设置"** 选项卡上，您需要在以下必填字段中输入信息。 必填字段用星号 (*) 表示。

|  字段        |  说明                                                               |
|---------------|----------------------------------------------------------------------------|
| **产品/服务 ID\***  | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 最大长度为 50 个字符。 它只能包含小写字母数字字符和破折号 （-）。 它不能以破折号结束。 此标识符在产品/服务生效后无法更改。 如果 Contoso 发布报价 ID，`sample-SvcApp`则为产品/服务分配了`https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource URL。      |
| **发布者\*** | 你的组织在 [AppSource](https://appsource.microsoft.com) 中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。                         |
| **名字\***      | 优惠的显示名称。 此名称将显示在 AppSource 和云合作伙伴门户上。 最大长度为 50 个字符。 使用产品可识别的品牌名称。 不要在此处包含组织的名称，除非应用使用该名称进行销售。 如果您在其他网站和出版物上提供此优惠，则在所有出版物中使用同一名称。    <br/>如果在 Power BI 应用的预览期内发布产品/服务，请在`(Preview)`应用程序名称的末尾添加字符串，如下所示： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>后续步骤

在下一个选项卡上，您将为产品/服务指定[技术信息](./cpp-technical-info-tab.md)。
