---
title: Azure Marketplace 的云合作伙伴门户中的虚拟机产品/服务设置选项卡
description: 介绍了用于创建 Azure 市场 VM 产品/服务的“产品/服务设置”选项卡。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938157"
---
# <a name="virtual-machine-offer-settings-tab"></a>虚拟机“产品/服务设置”选项卡

虚拟机的“新建产品/服务”页面在打开时将显示名为“产品/服务设置”的第一个选项卡。  

![虚拟机的“新建产品/服务”页面](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>产品/服务设置字段

在中**产品/服务设置**选项卡上，必须提供以下字段。  名称旁边附有星号 (*) 的字段表示必填字段。 

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **产品/服务 ID\***   | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 它的最大长度为 50 个字符，只能包含小写字母数字字符和短划线 (-)，但不能以短划线结尾。 产品/服务推出后，无法更改此字段。 <br> 例如，如果 Contoso 发布了产品/服务 ID 为 **sample-vm** 的产品/服务，则会为该产品/服务分配 Azure 市场 URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`。 |
| **Publisher\***  | 你的组织在 Azure 市场中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，将无法修改此值。 |
| **名称\***       | 产品/服务的显示名称。 此名称将显示在 Azure 市场和云合作伙伴门户中。 该名称不能超过 50 个字符。 指导原则是在其中包含产品的可识别品牌名称。 不要在此处包括你的组织的名称，除非那是产品的营销方式。 如果你在其他网站和出版物中营销此产品/服务，请确保名称在所有出版物中相同。 |
|   |   |
 
单击**保存**具有提供的所有字段之后。 


## <a name="next-steps"></a>后续步骤

在下一个选项卡上，你将添加产品/服务的 [SKU](./cpp-skus-tab.md)。
