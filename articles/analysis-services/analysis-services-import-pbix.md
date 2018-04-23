---
title: 将 Power BI Desktop 文件导入到 Azure Analysis Services 中 | Microsoft Docs
description: 介绍了如何使用 Azure 门户导入 Power BI Desktop 文件 (pbix)。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>导入 Power BI Desktop 文件

可以通过导入 Power BI Desktop 文件 (pbix) 在 Azure AS 中创建新模型。 将导入模型元数据、缓存的数据以及数据源连接。 不会导入报表和可视化效果。

**限制**   
- pbix 模型只能连接到 Azure SQL 数据库和 Azure SQL 数据仓库数据源。 
- pbix 模型不能具有实时连接或 DirectQuery 连接。 
- 如果 pbix 数据模型包含 Analysis Services 不支持的元数据，导入可能会失败。

## <a name="to-import-from-pbix"></a>从 pbix 中导入

1. 在服务器的“概述” > “Web 设计器”中，单击“打开”。

    ![在 Azure 门户中创建模型](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. 在“Web 设计器” > “模型”中，单击“+ 添加”。

    ![在 Azure 门户中创建模型](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. 在“新建模型”中，键入模型名称，然后选择 Power BI Desktop 文件。

    ![Azure 门户中的“新建模型”对话框](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. 在“导入”中，找到并选择你的文件。

     ![Azure 门户中的“连接”对话框](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>另请参阅

[在 Azure 门户中创建模型](analysis-services-create-model-portal.md)   
[连接到 Azure Analysis Services](analysis-services-connect.md)  
