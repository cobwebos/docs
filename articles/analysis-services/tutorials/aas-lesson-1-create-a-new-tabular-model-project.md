---
title: "Azure Analysis Services 教程第 1 课：创建新的表格模型项目 | Microsoft Docs"
description: "介绍如何创建新的 Azure Analysis Services 教程项目。"
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
ms.date: 06/01/2017
ms.author: owend
ms.openlocfilehash: d523e3e103b4c351d01af6f1eb3c396f9a63016a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-1-create-a-tabular-model-project"></a>第 1 课：创建表格模型项目

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

本课程使用 SQL Server Data Tools (SSDT) 创建 1400 兼容级别的新表格模型项目。 创建新项目后，可以开始添加数据和创作模型。 本课程还提供 SSDT 中表格模型创作环境的简介。  
  
本课预计完成时间：**10 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格模型创作教程的第一课。 若要完成本课程，需要满足一些先决条件。 有关详细信息，请参阅 [Azure Analysis Services - Adventure Works 教程](../tutorials/aas-adventure-works-tutorial.md)。  
  
## <a name="create-a-new-tabular-model-project"></a>创建新的表格模型项目  
  
#### <a name="to-create-a-new-tabular-model-project"></a>创建新的表格模型项目  
  
1.  在 SSDT 的“文件”菜单中，单击“新建” > “项目”。  
  
2.  在“新建项目”对话框中，展开“已安装” > “商业智能” > “Analysis Services”，并单击“Analysis Services 表格项目”。  
  
3.  在“名称”中键入 **AW Internet Sales**，并指定项目文件的位置。  
  
    默认情况下，解决方案名称与项目名称相同；但是，可以键入不同的解决方案名称。  
  
4.  单击 **“确定”**。  
  
5.  在“表格模型设计器”对话框中，选择“集成的工作区”。  
  
    在创作模型期间，工作区托管与项目同名的表格模型数据库。 集成的工作区表示 SSDT 使用内置实例，因此不需要仅仅出于模型创作目的单独安装一个 Analysis Services 服务器实例。
      
6.  在“兼容性级别”中，选择“SQL Server 2017/Azure Analysis Services (1400)”。   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    如果“兼容性级别”列表框中未显示“SQL Server 2017/Azure Analysis Services (1400)”，则表示使用的不是最新版本的 SQL Server Data Tools。 若要获取最新版本，请参阅[安装 SQL Server Data tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>了解 SSDT 表格模型创作环境  
创建新的表格模型项目后，让我们花点时间了解 SSDT 中的表格模型创作环境。  
  
创建项目后，它会在 SSDT 中打开。 右侧的“表格模型资源管理器”显示了模型中对象的树状视图。 由于尚未导入数据，因此文件夹是空的。 可以右键单击某个对象文件夹来执行操作，就像使用菜单栏一样。 在学习本教程的过程中，将使用“表格模型资源管理器”来浏览模型项目中的不同对象。

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

单击“解决方案资源管理器”选项卡。此处将显示 Model.bim 文件。 如果左侧未显示设计器窗口（包含 Model.bim 选项卡的空窗口），请在“解决方案资源管理器”中的“AW Internet Sales 项目”下面双击“Model.bim”文件。 Model.bim 文件包含模型项目的元数据。 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
单击“Model.bim”。 “属性”窗口中显示了模型属性，最重要的属性是“DirectQuery 模式”。 此属性指定模型是否在内存中模式（关闭）或 DirectQuery 模式（打开）下部署。 本教程将在内存中模式下创作并部署模型。

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
创建模型项目时，会根据可在“工具”菜单 >“选项”对话框中指定的“数据建模”设置，自动设置某些模型属性。 “数据备份”、“工作区保留”和“工作区服务器”属性指定如何以及在何处备份、在内存中保留和构建工作区数据库（模型创作数据库）。 以后可以根据需要更改这些设置，但目前需将这些属性保留原样。  

在“解决方案资源管理器”中，右键单击“AW Internet Sales”（项目），并单击“属性”。 此时会显示“AW Internet Sales 属性页”对话框。 稍后在部署模型时，可设置其中的某些属性。  
  
安装 SSDT 时，已将一些新的菜单项添加到 Visual Studio 环境。 单击“模型”菜单。 可在此处导入数据、刷新工作区数据、在 Excel 中浏览模型、创建透视图和角色、选择模型视图，以及设置计算选项。 单击“表”菜单。 可在此处创建和管理关系、指定日期表设置、创建分区，以及编辑表属性。 如果单击“列”菜单，可在表中添加和删除列、冻结列，以及指定排序顺序。 SSDT 还会在栏中添加一些按钮。 最有用的功能是“自动求和”，可为选定的列创建标准聚合度量值。 使用其他工具栏按钮可以快速访问常用的功能和命令。  
  
浏览专门用于创作表格模型的各种功能的一些对话框和位置。 尽管某些项尚未激活，但可以从中大致了解表格模型的创作环境。  
  

## <a name="whats-next"></a>后续步骤
[第 2 课：获取数据](../tutorials/aas-lesson-2-get-data.md)

  
  
  
