---
title: Azure 容器映像的产品/服务设置 |Azure Marketplace
description: 配置 Azure 容器的产品/服务设置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 61d9fa535d2bec0a52351ba6199183ea899a0e1c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146263"
---
# <a name="container-offer-settings-tab"></a>容器的“产品/服务设置”选项卡

> [!IMPORTANT]
> 从2020年4月13日开始，我们开始将 Azure 容器产品/服务的管理转移到合作伙伴中心。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 Azure 容器服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)中的说明来管理迁移的产品/服务。

“容器”>“新建产品/服务”**** 页打开时将聚焦在“产品/服务设置”选项卡。**** 

![套餐标识](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>“产品/服务标识”设置

在 "**产品标识**" 下，必须为下表中描述的字段提供信息。 名称旁边附有星号 (*) 的字段表示必填字段。 

|  **字段**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **产品/服务 ID\***       | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL 和见解报表中。 它的最大长度为 50 个字符，并且可以使用小写字母数字字符和短划线 (-)。 （标识符不能以短划线结尾。）**注意：** 在产品/服务推出后，不能更改此字段。 <br> 例如，如果 Contoso 发布了产品/服务 ID 为 **sample-container** 的产品/服务，则会为该产品/服务分配 Azure 市场 URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`。 |
| **发布者 ID\***     | 你的组织在 Azure 市场中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。 |
| **名称\***          | 产品/服务的显示名称。 此名称显示在 Azure 市场和云合作伙伴门户中。 该名称不能超过 50 个字符。 建议为你的产品使用可识别的品牌名称。 不要包含组织的名称，除非这是产品的营销方式。 如果你在其他网站和出版物中营销此产品/服务，请确保名称在所有出版物中相同。 |
|  |  |

选择 "**保存**" 以保存产品/服务设置。


## <a name="next-steps"></a>后续步骤

使用 [SKU](./cpp-skus-tab.md) 选项卡配置你的产品/服务的 SKU。
