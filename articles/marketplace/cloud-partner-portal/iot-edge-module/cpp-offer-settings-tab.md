---
title: 提供 Azure IoT 边缘模块的设置 |Azure 应用商店
description: 配置 IoT Edge 模块的产品/服务设置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7a35277a61217dab2982d6436903cd09abfe8c7d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984957"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge 模块“产品/服务设置”选项卡

>[!Important]
>从 2020 年 4 月 13 日起，我们将开始将 IoT Edge 模块产品的管理转移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[创建 IoT Edge 模块产品/服务](https://aka.ms/AzureCreateIoT)中的说明进行操作，以管理迁移的优惠。

“IoT Edge 模块”>“新建产品/服务”**** 页面打开时将聚焦在“产品/服务设置”选项卡。**** 

![IoT Edge 模块的“新建产品/服务”页面](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>“产品/服务标识”设置

在 **"优惠标识**"下，必须为下表中描述的字段提供信息。 名称旁边附有星号 (*) 的字段表示必填字段。 

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
| **产品/服务 ID\***       | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL 和见解报表中。 它的最大长度为 50 个字符，并且可以使用小写字母数字字符和短划线 (-)。 （标识符不能以破折号结尾。**注：** 此字段在产品/服务生效后无法更改。 <br> 例如，如果 Contoso 发布了产品/服务 ID 为 **sample-iot-edge-module** 的产品/服务，则会为该产品/服务分配 Azure 市场 URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`。 |
| **发布者\***     | 你的组织在 Azure 市场中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。 |
| **名称\***          | 产品/服务的显示名称。 此名称显示在 Azure 市场和云合作伙伴门户中。 该名称不能超过 50 个字符。 建议为你的产品使用可识别的品牌名称。 不要包括组织的名称，除非这是您的产品销售方式。 如果你在其他网站和出版物中营销此产品/服务，请确保名称在所有出版物中相同。 |
|  |  |


选择 **"保存"** 以保存产品/服务设置。


## <a name="next-steps"></a>后续步骤

使用 [SKU](./cpp-skus-tab.md) 选项卡配置你的产品/服务的 SKU。
