---
title: 更新现有的 Azure 容器产品/服务 |Azure Marketplace
description: 如何更新 Azure 市场中的现有容器产品/服务。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 0e0db961dec69880eff6ef640e9210af6fcbc978
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942591"
---
# <a name="update-an-existing-container-offer"></a>更新现有的容器组

本文分步介绍了在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新容器产品/服务的各个方面。

我们可能会出于多个原因更新套餐，例如：

-  向现有 SKU 添加新的容器映像版本。
-  添加新的 SKU。
-  更新套餐或单个 SKU 的市场元数据。

为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>不允许对容器产品/服务或 SKU 进行的更改

在 Azure 市场中推出产品/服务后，无法更改容器产品/服务或 SKU 的某些属性。 不能更改以下设置：

-  套餐的“套餐 ID”和“发布者 ID”
-  现有 SKU 的“SKU ID”
-  版本标记，例如 `1.0.1`
-  对现有 SKU 的计费/许可证模型更改

## <a name="common-update-operations"></a>常见更新操作

以下更新操作很常见。

### <a name="update-container-image-version-for-a-sku"></a>更新 SKU 的容器映像版本

我们经常会使用安全修补程序、其他功能等定期更新容器映像。 在这种情况下，需要使用以下步骤来更新你的 SKU 引用的容器映像：

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有套餐”下，找到要更新的套餐。
3. 在“SKU”选项卡中，选择与要更新的容器映像关联的 SKU。
4. 在“容器映像”下，选择“+ 新建映像版本”以添加新的容器映像。
5. 提供新的容器**映像版本**。 映像版本需要遵循与以前版本相同的标记准则。 版本标记应采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。 检查提供的新版本是否高于以前的所有版本。
6. 选择“发布”以启动将新映像版本发布到 Azure 市场的工作流。

### <a name="add-a-new-sku"></a>添加新 SKU

使用以下步骤使新 SKU 可用于套餐：

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有套餐”下，找到要更新的套餐。
3. 在“SKU”选项卡下，选择“添加新 SKU”并在弹出窗口中提供一个 **SKU ID**。
4. 使用[发布容器产品/服务](./cpp-publish-offer.md)中描述的步骤发布容器。
5. 选择“发布”，启动发布新 SKU 的工作流。

### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据。 （例如：公司名称、徽标，等等）

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有产品/服务”下，找到要更新的产品/服务。
3. 转到“市场”选项卡。使用[发布容器产品/服务](./cpp-publish-offer.md)中的说明进行元数据更改。
4. 选择“发布”，启动发布更改的工作流。

## <a name="compare-feature"></a>“比较”功能

对已发布的产品/服务进行更改时，可以使用“比较”功能来审核所做的更改。

### <a name="to-use-the-compare-feature"></a>若要使用“比较”功能，请执行以下操作：

1. 在编辑过程中的任意时间点，针对你的产品/服务选择“比较”。
2. 并列查看营销资产和元数据的版本。


## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看历史发布活动，请选择云合作伙伴门户左侧导航菜单栏中的“历史记录”选项卡。 可以看到在 Azure 市场套餐生存期内执行的带时间戳的操作。