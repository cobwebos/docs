---
title: 创建市场套餐 - Azure 市场 | Microsoft Docs
description: 使用云合作伙伴门户在 Azure 和 AppSource 市场中创建套餐
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
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
ms.date: 01/10/2019
ms.author: pbutlerm
ms.openlocfilehash: af9b34d90098409135020fa8a45ecd0253f25b22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454538"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>创建 Azure 市场和 AppSource 套餐

云合作伙伴门户的基本用途之一是让发布者创建套餐，然后将其发布到 Microsoft Azure 和 AppSource 市场。  若要执行此操作，始终需要先从[“新建套餐”菜单](../portal-tour/cpp-new-offer-menu.md)中选择所需的套餐类型。  在响应中，会显示该套餐类型对应的“新建套餐”页。  例如，下图显示了 Azure 应用程序类型的默认“新建套餐”页。

![“新建套餐”默认页](./media/new-offer-page.png)

此页面顶部附近显示的横向菜单栏中提供了两个选项卡选项： 
- “编辑器”选项卡 - 用于输入信息，以及上传新套餐实例的资产。  默认会显示此选项卡。
- “状态”选项卡 - 提供发布状态，并列出任何验证问题和评审问题。 

创建套餐时，请使用“编辑器”选项卡输入有关该套餐的信息。 

## <a name="editing-operations"></a>编辑操作

位于数据输入区域上方的横向工具栏显示以下按钮：

|   按钮    |   目的                                                          |
|   ------    |  --------                                                          |
| **保存**    | 保存最近所做的任何数据输入更改。  在离开该页面之前，必须手动保存更改，否则更改将会丢失。 | 
| **丢弃** | 丢弃最近所做的数据输入更改（自上次保存以来）             |
| **比较** | 将当前套餐的状态与已发布套餐的状态进行比较。  只有在成功发布套餐之后才会启用此选项。  |
| **发布** | 开始此套餐的发布过程                       |
| **删除**  | 创建套餐之后、发布套餐之前删除此套餐。 |
|   |   |


## <a name="editing-tabs"></a>编辑选项卡

创建套餐时，请在“新建套餐”页的左侧纵栏中的每个选项卡上提供必需和可选的数据。  将显示标准用户界面控件（例如文本框、下拉菜单和复选框）用于收集数据。  具体显示哪些编辑选项卡取决于套餐类型，下表列出了一些常用的选项卡。

|      选项卡名称       |   目的                                                            |
|      --------       |   -------                                                            |
| **产品/服务设置**  | 收集套餐信息和发布者身份信息。                    |
| SKU            | 定义套餐的每个存货单位 (SKU) 版本的技术和业务特征 |
| **体验版**      | 对于支持此可选功能的类型，请定义套餐的演示。  有关详细信息，请参阅[什么是体验版？](../test-drive/what-is-test-drive.md)  |
| “市场”或“店面” | 收集文本字符串、文档和图像用于列出市场中的套餐 |
| **支持**         | 收集客户、工程人员和在线支持人员的联系信息  |
|  |  |

名称类似的选项卡的内容根据套餐类型的不同而异。  这些选项卡的套餐特定详细信息在每个套餐类型的“创建套餐”部分中提供。


## <a name="next-steps"></a>后续步骤

创建并保存套餐之后，以及在发布套餐之前或之后，都可以[查看其状态](./cpp-view-status-offer.md)。
