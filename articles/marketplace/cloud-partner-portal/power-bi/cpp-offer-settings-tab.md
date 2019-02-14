---
title: Power BI 应用套餐的套餐设置 - Azure 市场 | Microsoft Docs
description: 为 Microsoft AppSource 市场配置 Power BI 应用套餐的套餐设置。
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
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665808"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 应用套餐设置选项卡

服务应用的“新建套餐”页在名为“套餐设置”的第一个选项卡中的打开。  在此选项卡中提供套餐的主要标识符和名称。名称旁边附有星号 (*) 的字段表示必填字段。

![“产品/服务设置”选项卡](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>套餐设置字段 

在“产品/服务设置”选项卡上，必须提供以下必需字段。

|  字段        |  说明                                                               |
|---------------|----------------------------------------------------------------------------|
| **产品/服务 ID**  | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将在产品 URL、资源管理器模板和计费报表中显示。 它的最大长度为 50 个字符，只能包含小写字母数字字符和短划线 (-)，但不能以短划线结尾。 产品/服务推出后，无法更改此字段。 例如，如果 Contoso 发布了套餐 ID 为 `sample-SvcApp` 的套餐，则会为该套餐分配 AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **发布者** | 你的组织在 [AppSource](https://appsource.microsoft.com) 中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，将无法修改此值。                         |
| **名称**      | 产品/服务的显示名称。 此名称将在 AppSource 和云合作伙伴门户中显示。 该名称不能超过 50 个字符。 指导原则是在其中包含产品的可识别品牌名称。 除非是应用的营销方式，否则不要在此处包括你的组织的名称。 如果在其他网站和出版物中营销此套餐，请确保名称在所有出版物中相同。    <br/>如果在 Power BI 应用的预览期间在预览模式下发布套餐，请将 `(Preview)` 字符串追加到应用程序的名称，例如 `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>后续步骤

在下一选项卡中，将指定套餐的[技术信息](./cpp-technical-info-tab.md)。
