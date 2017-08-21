---
title: "如何查看 Azure 数据目录中的相关数据资产 | Microsoft Docs"
description: "本文介绍如何在 Azure 数据目录中查看所选数据资产的相关数据资产。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ae028d3fafe0afac859055a61e2d5a154676f1b2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>如何查看 Azure 数据目录中的相关数据资产？
借助 Azure 数据目录，可以查看与所选数据资产相关的数据资产，并查看它们之间的关系。 

## <a name="supported-data-sources"></a>支持的数据源 
从下列数据源注册数据资产时，Azure 数据目录自动注册有关选定数据资产之间联接关系的元数据。 

- SQL Server
- Azure SQL 数据库
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>查看相关数据资产
若要查看与所选数据集相关的数据资产，请使用下图所示的“关系”选项卡： 

![Azure 数据目录 - 查看相关数据资产](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

在此示例中，所选 ProductSubcategory 数据资产之间存在两种关系： 

- 产品表的 ProductSubcategoryID 列与所选 ProductSubcategory 表的 ProductSubcategoryID 列之间具有外键关系。 
- ProductSubCategory 表的 ProductCategoryID 列与所选 ProductCategory 表的 ProductCategoryID 列之间具有外键关系。

> [!NOTE]
> 注意关系树状视图中箭头的方向。  

若要查看更多详细信息（如列的完全限定名），请将鼠标移动到其上，此时会出现一个弹出窗口，如下图所示： 

![Azure 数据目录 - 关系弹出窗口](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

若要包括已注册资产之间的关系，请重新注册这些资产。

## <a name="next-steps"></a>后续步骤
- [How to manage data assets](data-catalog-how-to-manage.md)
