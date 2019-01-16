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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073232"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure 市场的现有产品 
==============================================

产品/服务推出后可能需要对其进行各种更新，例如：

1.  将新的\"包\"添加到现有 SKU
2.  将新 SKU 添加到现有产品/服务
3.  更新产品/服务/SKU 市场元数据

你将使用云合作伙伴门户来实现这些更改，然后重新发布产品/服务。 本文将引导你完成更新 Azure 应用程序产品/服务的方方面面。

<a name="unpermitted-changes-to-azure-application-offer"></a>Azure 应用程序产品/服务的未经许可更改 
-----------------------------------------------

产品/服务在 Azure 市场中推出后，Azure 应用程序产品/服务/SKU 的一些属性将无法修改。

* 产品 ID 和产品发布者 ID。
* 现有 SKU 的 SKU ID。
* 更新已发布的包。

<a name="adding-a-new-package-to-an-existing-sku"></a>将新的包添加到现有 SKU 
---------------------------------------

发布者可能希望添加新版本的包以更新现有包。 可以通过上传具有不同版本号的新包来完成此添加。

1.  登录到[云合作伙伴门户](http://cloudpartner.azure.com)
2.  在“所有产品/服务”中，找到要更新的产品/服务
3.  在“SKU”窗体中，单击要更新其包的 SKU
4.  单击“新建包”\"\"并提供新版本
5.  上传包含更新的模板文件的新 .zip 文件
6.  单击“发布”开始发布工作流，推出新的 SKU。

<a name="adding-a-new-sku-to-an-existing-offer"></a>将新 SKU 添加到现有产品/服务
-------------------------------------

可以选择通过以下步骤使新 SKU 可用于现有产品/服务：

1.  登录到[云合作伙伴门户](http://cloudpartner.azure.com)
2.  在“所有产品/服务”中，找到要更新的产品/服务
3.  在“SKU”窗体中，单击“添加新 SKU”并在弹出窗口中提供一个 SKU ID。
4.  按照[此处](./cloud-partner-portal-managed-app-publish.md)指定的其余步骤操作。
5.  单击“发布”开始发布工作流，推出新的 SKU。

<a name="updating-offer-marketplace-metadata"></a>更新产品/服务市场元数据 
-----------------------------------

在一些情况下，需要更新与产品关联的市场元数据，例如更新公司徽标等。请按照以下步骤进行操作。

1.  登录到云合作伙伴门户。
2.  在“所有产品/服务”中，找到要更新的产品/服务
3.  转到“市场”表单，按照[此处](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md)的说明进行更改。
4.  单击“发布”开始发布工作流，使更改生效。
