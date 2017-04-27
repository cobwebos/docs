---
title: "更新 Azure 应用商店的现有产品 | Microsoft Docs"
description: "本文介绍通过云合作伙伴门户更新现有产品的详细信息"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 48424cb03c7fa521a2556ff00c2136eb5fae38ad
ms.lasthandoff: 04/12/2017


---
# <a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure 应用商店的现有产品
产品推出后可能需要对其进行各种更新。

1. 将新的 VM 映像版本添加到现有 SKU。
1. 更改 SKU 的可用区域。 
1. 添加新的 SKU。 
1. 更新产品/SKU 应用商店元数据。

为此，需在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新产品并进行重新发布。 本文将提供 VM 产品更新方方面面的分步指导。

## <a name="unpermitted-changes-to-vm-offersku"></a>VM 产品/SKU 的未经许可更改
产品在 Azure 应用商店推出后，VM 产品/SKU 的一些属性无法修改。
1. 产品 ID 和产品发布者 ID。
2. 现有 SKU 的 SKU ID。
3. 打开现有 SKU 的端口配置。
4. 现有 SKU 的数据磁盘计数。
5. 现有 SKU 的计费/许可证模型更改。

## <a name="updating-the-vm-image-version-for-a-sku"></a>更新 SKU 的 VM 映像版本
产品的 SKU 的 VM 映像可能已更新其他功能、安全修补程序等。在这些情况下，可能需要更新 SKU 所引用的 VM 映像。 为此，请执行以下步骤：
1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)
2. 在“所有产品”下找到要更新的产品
3. 在“SKU”窗体中，单击要更新的 VM 映像的 SKU
4. 在“磁盘版本”下，单击“+添加磁盘版本”，添加新的 VM 映像
5. 提供新的 VM 映像**磁盘版本**。 此磁盘版本需遵循[语义版本](http://semver.org/)格式。 版本格式应为 X.Y.Z，X、Y 和 Z 是整数。 确保提供的新版本高于先前版本，否则重新发布时不会显示在 Azure 门户或 Azure 应用商店中。
6. 对于“OS VHD URL”，输入为操作系统 VHD 创建的[共享访问签名 URI](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images)。 请注意，不同 SKU 版本间无法更改数据磁盘计数。 如果先前版本配置了数据磁盘，则此新版本也必须配置数据磁盘。 
7. 单击“发布”开始发布工作流，在 Azure 应用市场和 Azure 门户中推出新的 VM 版本。 

## <a name="changing-regions-a-sku-is-available-in"></a>更改 SKU 的可用区域
以后可能需要在更多区域提供产品/SKU。 或者，可能需要在特定区域停止支持产品/SKU。 为此，请执行以下步骤。

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)
2. 在“所有产品”下找到要更新的产品
3. 在“SKU”窗体下，单击要更新其区域可用性的 SKU
4. 单击“国家/地区可用性”字段下的“选择国家/地区”按钮。 
5. 在区域可用性弹出菜单中，添加/删除要为此 SKU 选择的区域。
6. 单击“发布”开始发布工作流，更新 SKU 区域可用性。 

如果要在新区域中提供 SKU，可通过“导出定价数据”功能对此特定区域指定定价。 请注意，如果添加之前已可用的区域，则无法更新其定价，因为不允许更改定价。 

## <a name="adding-a-new-sku"></a>添加新的 SKU
可选择为现有产品提供新的 SKU。 为此，请执行以下步骤。

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)
2. 在“所有产品”下找到要更新的产品
3. 在“SKU”窗体下，单击“添加新 SKU”并在弹出菜单中提供一个 SKU ID。 
4. 按照[此处](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md)指定的其余步骤操作。
6. 单击“发布”开始发布工作流，推出新的 SKU。

## <a name="updating-offer-marketplace-metadata"></a>更新产品应用商店元数据。
在一些情况下，需要更新与产品关联的应用商店元数据，例如更新公司徽标等。为此，请执行以下步骤。

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)
2. 在“所有产品”下找到要更新的产品
3. 转到“应用商店”窗体，按照[此处](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md)的指示进行更改。 
4. 单击“发布”开始发布工作流，使更改生效。


