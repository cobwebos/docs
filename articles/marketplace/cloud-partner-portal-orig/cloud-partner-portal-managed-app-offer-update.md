---
title: 更新 Azure 市场的现有产品
description: 更新 Azure 市场的现有产品
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805588"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure 市场的现有产品 
==============================================

产品/服务推出后可能需要对其进行各种更新。

1.  将新的\"包\"添加到现有 SKU
2.  将新 SKU 添加到现有产品/服务
3.  更新产品/服务/SKU 市场元数据

需要在云合作伙伴门户中更新产品/服务并进行重新发布。 本文将提供 Azure 应用程序产品/服务更新方方面面的分步指导。

<a name="unpermitted-changes-to-azure-application-offersku"></a>Azure 应用程序产品/服务/SKU 的未经许可更改 
--------------------------------------------------

产品/服务在 Azure 市场中推出后，Azure 应用程序产品/服务/SKU 的一些属性将无法修改。

1.  产品 ID 和产品发布者 ID。
2.  现有 SKU 的 SKU ID。
3.  更新已发布的包。

<a name="adding-a-new-package-to-an-existing-sku"></a>将新的包添加到现有 SKU 
---------------------------------------

发布者可能希望添加新版本的包以更新现有包。 这可以通过上传具有不同版本号的新包来完成。

1.  登录到[云合作伙伴门户](http://cloudpartner.azure.com)
2.  在“所有产品/服务”中找到要更新的产品/服务
3.  在“SKU”表单中，单击要更新的包的 SKU
4.  单击“新建包”\"\"并提供新版本
5.  上传包含更新的模板文件的新 .zip 文件
6.  单击“发布”开始发布工作流，推出新的 SKU。

<a name="adding-a-new-sku-to-an-existing-offer"></a>将新 SKU 添加到现有产品/服务
-------------------------------------

可选择为现有产品提供新的 SKU。 为此，请执行以下步骤。

1.  登录到[云合作伙伴门户](http://cloudpartner.azure.com)
2.  在“所有产品/服务”中找到要更新的产品/服务
3.  在“SKU”表单中，单击“添加新 SKU”并在弹出窗口中提供一个 SKU ID。
4.  按照[此处](./cloud-partner-portal-managed-app-publish.md)指定的其余步骤操作。
5.  单击“发布”开始发布工作流，推出新的 SKU。

<a name="updating-offer-marketplace-metadata"></a>更新产品/服务市场元数据 
-----------------------------------

在一些情况下，需要更新与产品关联的市场元数据，例如更新公司徽标等。请按照以下步骤进行操作。

1.  登录到云合作伙伴门户
2.  在“所有产品/服务”中找到要更新的产品/服务
3.  转到“市场”表单，按照[此处](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging)的说明进行更改。
4.  单击“发布”开始发布工作流，使更改生效。
