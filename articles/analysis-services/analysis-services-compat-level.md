---
title: "Azure Analysis Services 中的数据模型兼容性级别 | Microsoft Docs"
description: "了解表格数据模型兼容性级别。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 41bf697cd1d91ea0ba56ed6fad0e5b9c442ecec2
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Analysis Services 表格模型的兼容性级别

“兼容性级别”是指 Analysis Services 引擎中特定于发布的行为。 兼容性级别的更改通常与 SQL Server 主要版本一致。 还会在 Azure Analysis Services 中实现这些更改，以保持平台间的奇偶校验。 兼容性级别更改还会影响表格模型中的可用功能。 例如，DirectQuery 和表格对象元数据的实现因兼容性级别而异。 

Azure Analysis Services 支持 1200 和 1400 兼容性级别的表格模型。

最新兼容性级别是 1400。 此级别与 SQL Server 2017 Analysis Services 一致。 1400 兼容性级别中的主要功能包括：

*  新的基础结构，用于在 TOM API 和 TMSL 脚本的支持下连接数据和将数据导入表格模型。 此新功能支持 Azure Blob 存储等其他数据源。
*  通过使用“获取数据”和 M 表达式实现数据转换和数据混合功能。
*  度量值支持 DAX 表达式的“详细信息行”属性。 此属性支持 Microsoft Excel 等客户端工具向下钻取到聚合报表中的详细数据。 例如，当用户查看某个区域和月份的总销售额时，他们可以查看关联订单的详细信息。 
*  表和列名称，以及其中数据的对象级别安全性。
*  不规则层次结构的增强支持。
*  性能和监视改进。
  
## <a name="set-compatibility-level"></a>设置兼容性级别 
 在 SSDT 中创建新的表格模型项目时，可以在“表格模型设计器”对话框中指定兼容性级别。 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 如果选择“不再显示此消息”选项，所有后续项目都会使用指定为默认值的兼容性级别。 可以在“工具” > “选项”中更改 SSDT 中的默认兼容性级别。  
  
 若要升级 SSDT 中现有的表格模型项目，请在“属性”窗口中设置“兼容性级别”属性。 请记住，升级兼容性级别的操作不可逆。
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中检查表格模型数据库的兼容性级别 
 在 SSMS 中，右键单击数据库名称 >“属性” > “兼容性级别”。  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>检查 SSMS 中服务器支持的兼容性级别  
 在 SSMS 中，右键单击服务器名称 >“属性” > “支持的兼容性级别”。  
  
 此属性指定在服务器上运行的数据库的最高兼容性级别（包括预览版）。 不可更改支持的兼容性级别。  

## <a name="next-steps"></a>后续步骤
  [在 Azure 门户中创建模型](analysis-services-create-model-portal.md)   
  [管理 Analysis Services](analysis-services-manage.md)  
