---
title: Power BI 应用套餐的套餐设置 - Azure 市场 | Microsoft Docs
description: 配置 Power BI 应用产品/服务在 Microsoft AppSource marketplace 产品/服务设置。
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
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727926"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 应用套餐设置选项卡

当打开**新建产品/服务**页上的服务应用首次看到**产品/服务设置**选项卡。此选项卡上提供的主要标识符和产品/服务的名称。星号 （*） 表示必填的字段。

![“产品/服务设置”选项卡](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>产品/服务设置字段 

上**产品/服务设置**选项卡，您需要以下必填字段中输入信息：

|  字段        |  描述                                                               |
|---------------|----------------------------------------------------------------------------|
| **产品/服务 ID**  | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 最大长度为 50 个字符。 它只能包含小写字母数字字符和短划线 （-）。 它不能以短划线结尾。 产品/服务推出后，无法更改此标识符。 如果 Contoso 发布与产品/服务产品/服务 ID `sample-SvcApp`，该产品/服务分配 AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **发布者** | 你的组织在 [AppSource](https://appsource.microsoft.com) 中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。                         |
| **名称**      | 产品/服务显示名称。 此名称将显示在 AppSource 上和云合作伙伴门户上。 最大长度为 50 个字符。 使用一个识别您的产品的品牌名称。 除非销售应用程序需要具有该名称不包括此处为你组织的名称。 如果您要对其他网站和发布提供此产品/服务，请在所有发布中使用相同的名称。    <br/>如果 Power BI 应用的发布产品/服务在预览期间，将字符串添加`(Preview)`末尾的应用程序的名称，如下： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>后续步骤

在下一个选项卡，您可以将指定[技术信息](./cpp-technical-info-tab.md)产品/服务。
