---
title: 教程 - 使用 Power BI Desktop 连接 Azure Analysis Services | Microsoft Docs
author: minewiskan
description: 了解如何从 Azure 门户获取 Analysis Services 服务器名称，然后使用 Power BI Desktop 连接到服务器。
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4d8c753f06e58fd1cce1c55eca213637cb70e436
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "73572318"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>教程：使用 Power BI Desktop 进行连接

在本教程中，你使用 Power BI Desktop 连接到服务器上的 adventureworks 示例模型数据库。 你完成的任务将模拟到模型的典型用户连接和基于模型数据创建基本报表。

> [!div class="checklist"]
> * 从门户获取服务器名称
> * 使用 Power BI Desktop 进行连接
> * 创建基本报表

## <a name="prerequisites"></a>必备条件

- 向服务器中[添加 adventureworks 示例模型数据库](../analysis-services-create-sample-model.md)。
- 具有 adventureworks 示例模型数据库的 [*读取*](../analysis-services-server-admins.md) 权限。
- [安装最新的 Power BI Desktop](https://powerbi.microsoft.com/desktop)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
在本教程中，登录到门户只是为了获取服务器名称。 通常，用户将从服务器管理员那里获取服务器名称。

登录[门户](https://portal.azure.com/)。

## <a name="get-server-name"></a>获取服务器名称
若要从 Power BI Desktop 连接到服务器，首先需要服务器名称。 可以从门户获取服务器名称。

在 **Azure 门户**中，单击“服务器”>“概述”   > “服务器名称”  ，并复制服务器名称。
   
   ![在 Azure 中获取服务器名称](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>在 Power BI Desktop 中连接

1. 在 Power BI Desktop 中，单击“获取数据”   > “Azure”   > “Azure Analysis Services 数据库”  。

   ![在“获取数据”中连接](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. 在“服务器”  中，粘贴服务器名称，然后在“数据库”  中输入 **adventureworks**，然后单击“确定”。 

   ![指定服务器名称和模型数据库](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. 在系统提示时输入凭据。 你输入的帐户必须至少具有 adventureworks 示例模型数据库的读取权限。

    adventureworks 模型将在 Power BI Desktop 中打开，并且“报表”视图中将显示一个空白报表。 “字段”列表中会显示所有非隐藏的模型对象。  连接状态显示在右下角。

4. 在“可视化效果”  中，选择“簇状条形图”  ，单击“格式”  （滚漆筒图标），然后启用“数据标签”。  

   ![可视化效果](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. 在“字段” > **Internet Sales** 表中，选择 **Internet Sales Total** 和 **Margin** 度量值。 在 **Product Category** 表中，选择 **Product Category Name**。

   ![完成报表](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    花费几分钟时间来探究 adventureworks 示例模型：创建不同的可视化效果并对数据和指标进行切片。 当对报表感到满意后，务必进行保存。

## <a name="clean-up-resources"></a>清理资源

如果不再需要报表，则不要保存它，如果已保存，则将文件删除。

## <a name="next-steps"></a>后续步骤
在本教程中，你已了解了如何使用 Power BI Desktop 连接到服务器上的数据模型并创建基本报表。 如果不熟悉如何创建数据模型，请参阅 SQL Server Analysis Services 文档中的 [Adventure Works Internet Sales 表格数据建模教程](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial)。