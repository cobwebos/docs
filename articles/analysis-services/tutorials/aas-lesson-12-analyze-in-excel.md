---
title: "Azure Analysis Services 教程第 12 课：在 Excel 中分析 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何使用“在 Excel 中分析”功能。"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: e257862a88d39b96360703117f544c43e82b0e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-12-analyze-in-excel"></a>第 12 课：在 Excel 中分析

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

本课中，将使用“在 Excel 中分析”功能打开 Microsoft Excel，自动创建到模型工作区的连接，并自动向工作表添加数据透视表。 “在 Excel 中分析”功能提供一种快速简单的方法在部署模型之前测试模型的功效。 本课中不执行任何数据分析。 本课的目的是让你（模型作者）熟悉可以用来测试模型设计的工具。   
  
若要完成本课，必须将 Excel 安装在 SSDT 所在的同一计算机上。
  
本课预计完成时间：5 分钟  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 11 课：创建角色](../tutorials/aas-lesson-11-create-roles.md)。  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>使用默认透视和 Internet 销售透视进行浏览  
在这些首要任务中，将使用默认透视（这包括所有模型对象）和“Internet 销售”透视浏览你之前创建的模型。 Internet 销售透视不包括 Customer 表对象。  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>使用默认透视进行浏览  
  
1.  单击“模型”菜单 >“在 Excel 中分析”。  
  
2.  在“在 Excel 中分析”对话框中，单击“确定”。  
  
    将在 Excel 中打开新的工作簿。 将使用当前用户帐户创建数据源连接并使用默认透视定义可查看的字段。 将自动向工作表添加一个数据透视表。  
  
3.  Excel 的“数据透视表字段列表”中，会出现“DimDate”和“FactInternetSales”度量值组。 还会显示“DimCustomer”、“DimDate”、“DimGeography”、“DimProduct”、“DimProductCategory”、“DimProductSubcategory”和“FactInternetSales”表及其各自的列。  
  
4.  关闭 Excel 且不保存工作簿。  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>使用 Internet 销售透视进行浏览  
  
1.  单击“模型”菜单，并单击“在 Excel 中分析”。  
  
2.  在“在 Excel 中分析”对话框中，使“当前 Windows 用户”保持选中状态，在“透视”下拉列表框中选择“Internet 销售”，并单击“确定”。 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  在 Excel 中，在“数据透视表字段”中，请注意，字段列表中未包括 DimCustomer 表。  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  关闭 Excel 且不保存工作簿。  
  
## <a name="browse-by-using-roles"></a>使用角色进行浏览  
对任何表格模型而言，角色都是非常重要的部分。 如果没有一个可将用户作为其成员添加的角色，用户则无法使用模型访问和分析数据。 “在 Excel 中分析”功能提供了一种方法来测试已定义的角色。  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>使用“销售经理”用户角色进行浏览  
  
1.  在 SSDT 中单击“模型”菜单，并单击“在 Excel 中分析”。  
  
2.  在“指定用于连接到模型的用户名或角色”中，选择“角色”，然后在下拉列表框中选择“销售经理”，然后单击“确定”。  
  
    将在 Excel 中打开新的工作簿。 会自动创建一个数据透视表。 “数据透视表字段列表”包括新模型中可用的所有数据字段。  
      
3.  关闭 Excel 且不保存工作簿。  
  
## <a name="whats-next"></a>后续步骤
转到下一课：[第 13 课：部署](../tutorials/aas-lesson-13-deploy.md)。

  
  
  
