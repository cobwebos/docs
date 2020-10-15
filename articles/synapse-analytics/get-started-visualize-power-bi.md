---
title: 教程：Azure Synapse Analytics 入门 - 使用 Power BI 可视化工作区数据
description: 在本教程中，你将了解如何创建 Power BI 工作区、链接 Azure Synapse 工作区，以及创建利用 Azure Synapse 工作区中的数据的 Power BI 数据集。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 40a2cd5898ede7fc30db9a8475c5ab7cc68095ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337630"
---
# <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据

在本教程中，你将了解如何创建 Power BI 工作区、链接 Azure Synapse 工作区，以及创建利用你的 Azure Synapse 工作区中的数据的 Power BI 数据集。 

## <a name="overview"></a>概述

根据纽约市出租车数据，我们在两个表中创建了聚合数据集：
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

可以将 Power BI 工作区链接到 Azure Synapse 工作区。 借助此功能，你可以轻松地将数据导入 Power BI 工作区。 可以直接在 Azure Synapse 工作区中编辑 Power BI 报表。

### <a name="create-a-power-bi-workspace"></a>创建 Power BI 工作区

1. 登录到 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 新建名为 NYCTaxiWorkspace1 的 Power BI 工作区。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>将 Azure Synapse 工作区链接到新的 Power BI 工作区

1. 在 Synapse Studio 中，转到“管理” > “关联的服务” 。
1. 选择“新建” > “连接到 Power BI”并设置以下字段 ：

    |设置 | 建议的值 | 
    |---|---|
    |**名称**|**NYCTaxiWorkspace1**|
    |**工作区名称**|**NYCTaxiWorkspace1**|

1. 选择“创建”。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>创建 Power BI 数据集，该数据集使用 Azure Synapse 工作区中的数据

1. 在 Synapse Studio 中，转到“开发” > “Power BI” 。
1. 转到“NYCTaxiWorkspace1” > “Power BI 数据集”，然后选择“新建 Power BI 数据集”  。
1. 将鼠标悬停在 SQLDB1 数据库上，然后选择“下载 .pbids 文件” 。
1. 打开下载的 .pbids 文件。 Power BI Desktop 将打开并自动连接到 Azure Synapse 工作区中的 SQLDB1。
1. 如果看到名为“SQL 服务器数据库”的对话框：
    1. 选择“Microsoft 帐户”。
    1. 选择“登录”并登录到你的帐户。
    1. 选择“连接”。
1. “导航器”对话框打开后，请检查 PassengerCountStats 表，然后选择“加载”  。
1. “连接设置”对话框显示后，请选择“DirectQuery” > “确定”  。
1. 选择左侧的“报表”按钮。
1. 向报表添加“折线图”。
    1. 将“PasssengerCount”列拖到“可视化效果” > “轴”  。
    1. 将“SumTripDistance”和“AvgTripDistance”列拖到“可视化效果” > “值”   。
1. 在“主页”选项卡上，选择“发布” 。
1. 选择“保存”以保存更改。
1. 选择文件名“PassengerAnalysis.pbix”，然后选择“保存” 。
1. 在“选择目标”中选择“NYCTaxiWorkspace1”，然后单击“选择”  。
1. 等待发布完成。

### <a name="configure-authentication-for-your-dataset"></a>为数据集配置身份验证

1. 打开 [powerbi.microsoft.com](https://powerbi.microsoft.com/)然后“登录”。
1. 在左侧的“工作区”下，选择 NYCTaxiWorkspace1 工作区 。
1. 在该工作区中，找到名为“乘客分析”的数据集和名为“乘客分析”的报表 。
1. 将鼠标悬停在 PassengerAnalysis 数据集上，选择省略号 (...) 按钮，然后选择“设置” 。
1. 在“数据源凭据”中，将“身份验证方法”设置为“OAuth2”，然后选择“登录”   。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中编辑报表

1. 返回到 Synapse Studio，然后选择“关闭并刷新”。
1. 转到“开发”中心。
1. 将鼠标悬停在 Power BI 上，然后选择以刷新“Power BI 报表”节点 。
1. 在 Power BI 下，你应会看到：
    * 在“NYCTaxiWorkspace1” > “Power BI 数据集”下，名为“PassengerAnalysis”的新数据集  。
    * 在“NYCTaxiWorkspace1” > “Power BI 报表”下，名为“PassengerAnalysis”的新报表  。
1. 选择 PassengerAnalysis 报表。 该报表随即打开，可以在 Synapse Studio 中直接对其进行编辑。

## <a name="monitor-activities"></a>监视活动

1. 在 Synapse Studio 中，转到“监视”中心。
1. 在此位置，你可以看到工作区中所有活动的历史记录，以及目前处于有效状态的活动。
1. 浏览“管道运行”、“Apache Spark 应用程序”和“SQL 请求”，以查看你已在工作区中执行的操作  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视](get-started-monitor.md)
                                 

