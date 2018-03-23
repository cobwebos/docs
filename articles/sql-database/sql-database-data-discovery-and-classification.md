---
title: Azure SQL 数据库数据发现和分类 | Microsoft Docs
description: Azure SQL 数据库数据发现和分类
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 18afebefaee9117244767e0a00e293a297bbf9af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Azure SQL 数据库数据发现和分类
数据发现和分类（当前为预览版）提供了内置于 Azure SQL 数据库的高级功能，可用于发现、分类、标记和保护数据库中的敏感数据。 & 
发现最敏感的数据（业务、财务、医疗保健、 PII 等）并进行分类可在组织的信息保护方面发挥关键作用。 它可以作为基础结构，用于：
* 帮助满足数据隐私标准和法规符合性要求，如 GDPR。
* 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
* 控制对包含高度敏感数据的数据库的访问并强化其安全性。

## <a id="subheading-1"></a>概述
数据发现和分类引入了一套高级的服务和新的 SQL 功能，形成了新的 SQL 信息保护模式，旨在保护数据，而不仅仅是数据库：
* **发现和建议** - 分类引擎扫描数据库，并识别包含潜在敏感数据的列。 使用此功能可以通过 Azure 门户轻松地查看和应用适当的分类建议。
* **标记** – 使用 SQL 引擎中引入的新分类元数据属性，可在列上永久标记敏感度分类标签。 然后，此元数据可用于基于敏感度的高级审核和保护方案。
* **查询结果集敏感度** – 实时计算查询结果集的敏感度以供审核。
* **可见性** - 在门户中的详细仪表板中可以查看数据库分类状态。 此外，还可以下载用于符合性和审核目的以及其他需求的报表（Excel 格式）。

## <a id="subheading-2"></a>发现、分类和标记敏感列
以下部分介绍有关发现、分类和标记含数据库中的敏感数据的列的步骤，以及查看数据库当前分类状态和导出报告的步骤。

分类包含两个元数据属性：
* 标签 - 主要分类属性，用于定义列中存储数据的敏感度级别。  
* 信息类型 - 为列中存储数据的类型提供其他粒度。

<br>
对 SQL 数据库进行分类：

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 导航到 SQL 数据库中的“数据发现和分类（预览版）”设置。

    ![导航窗格][1]

3. “概述”选项卡包含数据库当前分类状态的摘要，其中包括所有已分类列的详细列表，你还可以筛选此列表，仅查看特定的架构部分、信息类型和标签。 如果尚未对任何列进行分类，请[跳到步骤 5](#step-5)。

    ![导航窗格][2]

4. 要下载 Excel 格式的报表，请单击窗口顶部菜单中的“导出”选项。

    ![导航窗格][3]

5.  <a id="step-5"></a>要开始对数据进行分类，请单击窗口顶部的“分类”选项卡。

    ![导航窗格][4]

6. 分类引擎会扫描数据库，查找包含潜在敏感数据的列，并提供“建议列分类”列表。 查看并应用分类建议：

    * 要查看建议的列分类列表，请单击窗口底部的“建议”面板：

        ![导航窗格][5]

    * 查看建议列表 - 要接受特定列的建议，请选中相关行左侧列中的复选框。 还可以通过选中建议表格表头中的复选框，将所有建议标记为“已接受”。

        ![导航窗格][6]

    * 要应用所选建议，请单击蓝色的“接受所选建议”按钮。

        ![导航窗格][7]

7. 此处，还可选择手动分类列表，或者根据建议进行分类：

    * 单击窗口顶部菜单中的“添加分类”。

        ![导航窗格][8]

    * 在打开的“上下文”窗口中，选择“架构”>“表格”>“想要分类的列”，以及“信息类型”和“敏感度标签”。 然后单击“上下文”窗口底部的蓝色“添加分类”按钮。

        ![导航窗格][9]

8. 要完成分类并使用新的分类元数据永久标记数据库的列，请在窗口顶部菜单中单击“保存”。

    ![导航窗格][10]

## <a id="subheading-3"></a>审核对敏感数据的访问

信息保护范例的一个重要方面是能够监视对敏感数据的访问。

[Azure SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) 已经过增强，在审核日志中加入了名为 data_sensitivity_information 的新字段，该字段会记录查询返回的实际数据的敏感度分类（标签）。

![导航窗格][11]

## <a id="subheading-4"></a>后续步骤
请考虑配置 [Azure SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) 来监视和审核对已分类敏感数据的访问。

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
