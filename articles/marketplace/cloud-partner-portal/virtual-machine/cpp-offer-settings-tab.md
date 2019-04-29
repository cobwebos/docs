---
title: Azure 市场的云合作伙伴门户中的虚拟机“产品/服务设置”选项卡 | Microsoft Docs
description: 介绍了用于创建 Azure 市场 VM 产品/服务的“产品/服务设置”选项卡。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844308"
---
# <a name="virtual-machine-offer-settings-tab"></a>虚拟机“产品/服务设置”选项卡

虚拟机的“新建产品/服务”页面在打开时将显示名为“产品/服务设置”的第一个选项卡。  名称旁边附有星号 (*) 的字段表示必填字段。 

![虚拟机的“新建产品/服务”页面](./media/publishvm_004.png)

在“产品/服务设置”选项卡上，必须提供以下必需字段。

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **产品/服务 ID**       | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 它的最大长度为 50 个字符，只能包含小写字母数字字符和短划线 (-)，但不能以短划线结尾。 产品/服务推出后，无法更改此字段。 <br> 例如，如果 Contoso 发布了产品/服务 ID 为 **sample-vm** 的产品/服务，则会为该产品/服务分配 Azure 市场 URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`。 |
| **发布者**     | 你的组织在 Azure 市场中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，将无法修改此值。 |
| **名称**          | 产品/服务的显示名称。 此名称将显示在 Azure 市场和云合作伙伴门户中。 该名称不能超过 50 个字符。 指导原则是在其中包含产品的可识别品牌名称。 不要在此处包括你的组织的名称，除非那是产品的营销方式。 如果你在其他网站和出版物中营销此产品/服务，请确保名称在所有出版物中相同。 |
|  |  |
 
选择“保存”来保存进度。 在下一个选项卡上，你将添加产品/服务的 [SKU](./cpp-skus-tab.md)。
