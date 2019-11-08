---
title: 更新现有 Azure IoT Edge 模块产品 |Azure Marketplace
description: 如何更新 Azure 市场中的现有 IoT Edge 模块套餐。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: cd0167e1af5bf8ef667df88237d83e9f33ed41f9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813390"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>更新现有的 IoT Edge 模块套餐

本文从不同的方面逐步讲解如何在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新 IoT Edge 模块套餐，然后重新发布该套餐。

我们可能会出于多个原因更新套餐，例如：

-  将新的 IoT Edge 模块映像版本添加到现有 SKU。
-  添加新的 SKU。
-  更新套餐或单个 SKU 的市场元数据。

为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>不允许对 IoT Edge 模块套餐或 SKU 进行的更改

产品/服务或 SKU 在 Azure Marketplace 上生活后，不能更改 IoT Edge 模块产品或 SKU 的属性。 不能更改以下设置：

-  套餐的“套餐 ID”和“发布者 ID”
-  现有 SKU 的“SKU ID”
-  版本标记，例如 `1.0.1`
-  对现有 SKU 的计费/许可证模型更改

## <a name="common-update-operations"></a>常见更新操作

以下更新操作很常见。

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>更新 SKU 的 IoT Edge 模块映像版本

使用安全修补程序、附加功能等定期更新 IoT Edge 模块映像是很常见的。 在这种情况下，需要使用以下步骤来更新 SKU 引用的 IoT Edge 模块映像：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  在“SKU”选项卡中，选择与要更新的 IoT Edge 模块映像关联的 SKU。

4.  在“Edge 模块映像”下，选择“+ 新建映像版本”以添加新的 IoT Edge 模块映像。

5.  提供新 IoT Edge 模块的**映像版本**。 映像版本需要遵循与以前版本相同的标记准则。 版本标记应采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。 检查提供的新版本是否高于以前的所有版本。

6.  选择“发布”，启动将新 IoT Edge 模块版本发布到 Azure 市场的工作流。

### <a name="add-a-new-sku"></a>添加新 SKU

使用以下步骤使新 SKU 可用于套餐： 

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  在“SKU”选项卡下，选择“添加新 SKU”并在弹出窗口中提供一个 **SKU ID**。

4.  使用将[IoT Edge 模块发布到 Azure Marketplace](./cpp-publish-offer.md)中所述的步骤，重新发布 IoT Edge 模块。

5.  选择“发布”，启动发布新 SKU 的工作流。


### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据。 （例如：公司名称、徽标等）

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  请参阅 " **marketplace** " 选项卡。按照将[IoT Edge 模块发布到 Azure marketplace 一](./cpp-publish-offer.md)文中的说明进行元数据更改。

4.  选择“发布”，启动发布更改的工作流。

## <a name="compare-feature"></a>“比较”功能

对发布的套餐进行更改时，可以使用“比较”功能来审核所做的更改。 

**若要使用“比较”功能：**

1.  在编辑过程中的任意时间点，选择套餐旁边的“比较”。

    ![“比较”功能按钮](./media/iot-edge-module-compare.png)


2.  并列查看营销资产和元数据的版本。


## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看历史发布活动，请选择云合作伙伴门户左侧导航菜单栏中的“历史记录”选项卡。 可以看到在 Azure 市场套餐生存期内执行的带时间戳的操作。  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
