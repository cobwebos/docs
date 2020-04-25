---
title: Azure IoT Edge 模块的产品/服务设置 |Azure Marketplace
description: 配置 IoT Edge 模块的产品/服务设置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148036"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge 模块“产品/服务设置”选项卡

>[!Important]
>从2020年4月13日开始，我们将开始向合作伙伴中心移动 IoT Edge 模块的管理。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 IoT Edge 模块提供](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)中的说明来管理迁移的产品/服务。

“IoT Edge 模块”>“新建产品/服务”**** 页面打开时将聚焦在“产品/服务设置”选项卡。****

![IoT Edge 模块的“新建产品/服务”页面](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>“产品/服务标识”设置

在 "**产品标识**" 下，必须为下表中描述的字段提供信息。 名称旁边附有星号 (*) 的字段表示必填字段。 

|  **字段**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **产品/服务 ID\***       | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL 和见解报表中。 它的最大长度为 50 个字符，并且可以使用小写字母数字字符和短划线 (-)。 （标识符不能以短划线结尾。）**注意：** 在产品/服务推出后，不能更改此字段。 <br> 例如，如果 Contoso 发布了产品/服务 ID 为 **sample-iot-edge-module** 的产品/服务，则会为该产品/服务分配 Azure 市场 URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`。 |
| **发布者\***     | 你的组织在 Azure 市场中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。 |
| **名称\***          | 产品/服务的显示名称。 此名称显示在 Azure 市场和云合作伙伴门户中。 该名称不能超过 50 个字符。 建议为你的产品使用可识别的品牌名称。 不要包含组织的名称，除非这是产品的营销方式。 如果你在其他网站和出版物中营销此产品/服务，请确保名称在所有出版物中相同。 |
|  |  |


选择 "**保存**" 以保存产品/服务设置。


## <a name="next-steps"></a>后续步骤

使用 [SKU](./cpp-skus-tab.md) 选项卡配置你的产品/服务的 SKU。
