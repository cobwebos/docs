---
title: Power BI 应用产品/服务的产品/服务设置 |Azure Marketplace
description: 为 Microsoft AppSource marketplace 的 Power BI 应用提供配置产品/服务设置。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822680"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 应用套餐设置选项卡

当你为服务应用打开 "**新建产品**/服务" 页面时，你将首先看到 "产品/服务**设置**" 选项卡。在此选项卡上提供产品/服务的主要标识符和名称。星号（*）表示必填字段。

![“产品/服务设置”选项卡](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>产品/服务设置字段 

在 "**产品/服务设置**" 选项卡上，需要在以下必填字段中输入信息。 必填字段用星号 (*) 表示。

|  字段        |  说明                                                               |
|---------------|----------------------------------------------------------------------------|
| **产品/服务 ID\***  | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 最大长度为 50 个字符。 它只能包含小写字母数字字符和短划线（-）。 它不能以短划线结束。 产品/服务投入使用后，不能更改此标识符。 如果 Contoso 发布产品/服务 ID `sample-SvcApp`的产品/服务，则会为该产品/服务分配 AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **发布服务器\*** | 你的组织在 [AppSource](https://appsource.microsoft.com) 中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。                         |
| **名称\***      | 产品/服务的显示名称。 此名称将显示在 AppSource 和云合作伙伴门户上。 最大长度为 50 个字符。 使用可用于你的产品的品牌名称。 请不要在此处包含组织的名称，除非应用以该名称推销。 如果对其他网站和发布提供此优惠，请在所有发布中使用相同的名称。    <br/>如果在 Power BI 应用预览期间发布产品/服务，请在应用程序名称的末尾添加字符串 `(Preview)`，如下所示： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>后续步骤

在下一个选项卡上，你将为你的产品/服务指定[技术信息](./cpp-technical-info-tab.md)。
