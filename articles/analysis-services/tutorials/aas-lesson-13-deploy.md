---
title: "Azure Analysis Services 教程第 13 课：部署 | Microsoft Docs"
description: "介绍了如何将教程项目部署到 Azure Analysis Services。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 8e3e1be572aa66ab46f894a2e5f395d1e6f2ea23
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-13-deploy"></a>第 13 课：部署

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，你将配置部署属性；指定 Azure 中的 Analysis Services 服务器或本地 SQL Server vNext Analysis Services 服务器，以及模型的名称。 然后，你将模型部署到该实例。 在部署模型后，用户可以使用报告客户端应用程序连接到该模型。 有关详细信息，请参阅[部署到 Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)。  
  
本课预计完成时间：5 分钟  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，你应当已完成上一课：[第 12 课：在 Excel 中分析](../tutorials/aas-lesson-12-analyze-in-excel.md)。  

**重要说明：**如果你在本地 SQL Server 上安装了 AdventureWorksDW2014 示例数据库，并且要将模型部署到 Azure Analysis Services 服务器，则需要[本地数据网关](../analysis-services-gateway.md)。
  
## <a name="deploy-the-model"></a>部署模型  
  
#### <a name="to-configure-deployment-properties"></a>配置部署属性  

  
1.  在“解决方案资源管理器”中，右键单击“AW Internet 销售”项目，然后单击“属性”。  
  
2.  在“AW Internet 销售属性页”对话框中，在“部署服务器”下的“服务器”属性中输入 Azure 中的或本地的 Analysis Services 服务器的名称。  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
 
    > [!IMPORTANT]  
    > 你必须在远程 Analysis Services 实例上具有管理员权限才能向其进行部署。  
  
3.  在“数据库”属性中，键入“Adventure Works Internet 销售”。  
  
4.  在“模型名称”属性中，键入“Adventure Works Internet 销售模型”。  
  
5.  验证你的选择，然后单击“确定”。  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>部署“Adventure Works Internet 销售”
  
1.  在“解决方案资源管理器”中，右键单击“AW Internet 销售”项目，然后单击“生成”。  

2.  右键单击“AW Internet 销售”项目，然后单击“部署”。

    部署到 Azure Analysis Services 时，可能会提示你输入你的帐户。 输入你的组织帐户和密码，例如 nancy@adventureworks.com。 此帐户必须位于服务器实例上的管理员组中。
  
    “部署”对话框将会出现，其中显示了模型中包括的元数据和每个表的部署状态。  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. 当部署成功完成时，继续操作并单击“关闭”。  
  
## <a name="conclusion"></a>结束语  
祝贺你！ 你已完成了你的第一个 Analysis Services 表格模型的创作和部署。 本教程已指导你完成了创建表格模型时的最常见任务。 现在，你的“Adventure Works Internet 销售”模型已部署，你可以使用 SQL Server Management Studio 管理该模型，创建处理脚本和备份计划。 用户现在也可以使用报告客户端应用程序（例如 Microsoft Excel 或 Power BI）连接到该模型。  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>后续步骤
*  [补充课程 - 动态安全性](../tutorials/aas-supplemental-lesson-dynamic-security.md)

*  [补充课程 - 详细信息行](../tutorials/aas-supplemental-lesson-detail-rows.md)

*  [补充课程 - 不规则层次结构](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)

