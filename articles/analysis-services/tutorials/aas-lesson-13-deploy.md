---
title: Azure Analysis Services 教程第 13 课：部署 | Microsoft Docs
description: 介绍了如何将教程项目部署到 Azure Analysis Services。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9b953428525e7970fef7224e65200cf9811b6304
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596274"
---
# <a name="deploy"></a>部署

本课介绍如何配置部署属性；指定要部署到的 Azure Analysis Services 服务器，以及模型的名称。 然后，将模型部署到该实例。 在部署模型后，用户可以使用报告客户端应用程序连接到该模型。 有关详细信息，请参阅[部署到 Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)。  
  
本课预计完成时间：**5 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本文是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 12 课：在 Excel 中分析](../tutorials/aas-lesson-12-analyze-in-excel.md)。  

> [!IMPORTANT]  
> 必须在远程 Analysis Services 服务器上拥有[管理员权限](../analysis-services-server-admins.md)才能向其进行部署。  

> [!IMPORTANT]  
> 如果在本地 SQL Server 上安装了 AdventureWorksDW2014 示例数据库，并且要将模型部署到 Azure Analysis Services 服务器，则需要[本地数据网关](../analysis-services-gateway.md)。
  
## <a name="deploy-the-model"></a>部署模型  
  
#### <a name="to-configure-deployment-properties"></a>配置部署属性  

  
1.  在“解决方案资源管理器”中，右键单击“AW Internet 销售”项目，并单击“属性”。  
  
2.  在“AW Internet 销售属性页”对话框中，在“部署服务器”下的“服务器”属性中输入完整的服务器名称。  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  在“数据库”属性中，键入“Adventure Works Internet 销售”。  
  
4.  在“模型名称”属性中，键入“Adventure Works Internet 销售模型”。  
  
5.  验证你的选择，并单击“确定”。  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>部署“Adventure Works Internet 销售”
  
1.  在“解决方案资源管理器”中，右键单击“AW Internet 销售”项目，并单击“生成”。  

2.  右键单击“AW Internet 销售”项目，并单击“部署”。

    部署到 Azure Analysis Services 时，可能会提示输入帐户。 输入组织帐户和密码，例如 nancy@adventureworks.com。 此帐户必须位于服务器上的管理员组中。
  
    “部署”对话框会出现，其中显示了模型中包括的元数据和每个表的部署状态。  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. 当部署成功完成时，继续操作并单击“关闭”。  
  

本课程介绍如何使用最常用且最容易的方法通过 SSDT 部署表格模型。 高级部署选项（例如部署向导或“使用 XMLA 和 AMO 自动完成”）具有更好的灵活性、一致性和计划部署。 若要了解详细信息，请参阅[表格模型解决方案部署](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular)。

## <a name="conclusion"></a>结束语  
祝贺你！ 已完成了第一个 Analysis Services 表格模型的创作和部署。 本教程已指导完成了创建表格模型时的最常见任务。 现在，“Adventure Works Internet 销售”模型已部署，可以使用 SQL Server Management Studio 管理该模型，创建处理脚本和备份计划。 用户现在也可以使用报告客户端应用程序（例如 Microsoft Excel 或 Power BI）连接到该模型。  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>后续步骤
[使用 Power BI Desktop 进行连接](../analysis-services-connect-pbi.md)   
[补充课程 - 动态安全性](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[补充课程 - 详细信息行](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[补充课程 - 不规则层次结构](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
