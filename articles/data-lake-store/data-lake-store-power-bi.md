---
title: "使用 Power BI 分析 Data Lake Store 中的数据 | Microsoft Docs"
description: "使用 Power BI 分析 Azure Data Lake Store 中存储的数据"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 59f072c7a8272fc04e1d662c0ab17e7ee4500fa6
ms.openlocfilehash: f965e3a5067dd8dd2ac6dc29446c7f5b50cdd450
ms.contentlocale: zh-cn
ms.lasthandoff: 02/07/2017

---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>使用 Power BI 分析 Data Lake Store 中的数据
本文介绍如何使用 Power BI Desktop 分析和可视化 Azure Data Lake Store 中存储的数据。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 遵循[通过 Azure 门户实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md) 中的说明。 本文假定你已创建有名为 **mybidatalakestore** 的 Data Lake Store 帐户，且已向其中上传了示例数据文件 (**Drivers.txt**)。 可从 [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)（Azure Data Lake Git 存储库）下载此示例文件。
* **Power BI Desktop**。 可从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=45331) 进行下载。 

## <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中创建报表
1. 在计算机上启动 Power BI Desktop。
2. 在“主页”功能区上，单击“获取数据”，然后单击“详细信息”。 在“获取数据”对话框中，单击“Azure”，单击“Azure Data Lake Store”，然后单击“连接”。
   
    ![连接到 Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "连接到 Data Lake Store")
3. 如果出现一个对话框显示连接器处于开发阶段，选择继续。
4. 在“Microsoft Azure Data Lake Store”对话框中，向 Data Lake Store 帐户提供 URL，然后单击“确定”。
   
    ![Data Lake Store 的 URL](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Store 的 URL")
5. 在下一个对话框中，单击“登录”以登录到 Data Lake Store 帐户。 将重定向到组织的登录页面。 按照提示登录到该帐户。
   
    ![登录到 Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "登录到 Data Lake Store")
6. 登录成功后，单击“连接”。
   
    ![连接到 Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "连接到 Data Lake Store")
7. 下一个对话框中会显示已上传至 Data Lake Store 帐户的文件。 验证信息，然后单击“加载”。
   
    ![从 Data Lake Store 加载数据](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "从 Data Lake Store 加载数据")
8. 数据成功加载到 Power BI 后，“字段”选项卡会出现以下字段。
   
    ![已导入的字段](./media/data-lake-store-power-bi/imported-fields.png "已导入的字段")
   
    但是，若要可视化和分析数据，最好以下每个字段都可用该数据
   
    ![所需字段](./media/data-lake-store-power-bi/desired-fields.png "所需字段")
   
    接下来的步骤会更新查询，以按所需格式转换已导入的数据。
9. 在“主页”功能区上，单击“编辑查询”。
   
    ![编辑查询](./media/data-lake-store-power-bi/edit-queries.png "编辑查询")
10. 在“查询编辑器”中的“内容”列，单击“二进制”。
    
    ![编辑查询](./media/data-lake-store-power-bi/convert-query1.png "编辑查询")
11. 会出现一个文件图标，该图标表示已上传的 **Drivers.txt** 文件。 右键单击该文件，然后单击“CSV”。    
    
    ![编辑查询](./media/data-lake-store-power-bi/convert-query2.png "编辑查询")
12. 会出现如下所示的输出。 数据现在已是可用于创建可视化的格式。
    
    ![编辑查询](./media/data-lake-store-power-bi/convert-query3.png "编辑查询")
13. 在“主页”功能区上，单击“关闭并应用”，然后单“关闭并应用”。
    
    ![编辑查询](./media/data-lake-store-power-bi/load-edited-query.png "编辑查询")
14. 查询更新后，“字段”选项卡会显示可用于可视化的新字段。
    
    ![已更新的字段](./media/data-lake-store-power-bi/updated-query-fields.png "已更新的字段")
15. 创建一个表示特定国家/地区的每个城市中司机的饼图。 为此，请如下进行选择。
    
    1. 在“可视化”选项卡中，单击饼图符号。
       
        ![创建饼图](./media/data-lake-store-power-bi/create-pie-chart.png "创建饼图")
    2. 将使用到的列是“列 4”（城市名称）和“列 7”（国家/地区名称）。 如下所示，从“字段”选项卡将这些列拖动到“可视化”选项卡。
       
        ![创建视觉效果](./media/data-lake-store-power-bi/create-visualizations.png "创建视觉效果")
    3. 此时饼图应如下所示。
       
        ![饼图](./media/data-lake-store-power-bi/pie-chart.png "创建视觉效果")
16. 通过在页面级别筛选器中选择一个特定的国家/地区，此时会显示所选国家/地区的每个城市中的司机数量。 例如，在“可视化”选项卡中的“页面级别筛选器”中，选择“巴西”。
    
    ![选择国家/地区](./media/data-lake-store-power-bi/select-country.png "选择国家/地区")
17. 饼图将自动更新，显示巴西各个城市中的司机。
    
    ![某个国家/地区中的司机](./media/data-lake-store-power-bi/driver-per-country.png "每个国家/地区的司机")
18. 在“文件”菜单上，单击“保存”将可视化对象另存为 Power BI Desktop 文件。

## <a name="publish-report-to-power-bi-service"></a>将报表发布到 Power BI 服务
在 Power BI Desktop 中创建可视化后，可将其发布到 Power BI 服务中与他人共享。 有关如何发布的说明，请参阅[从 Power BI Desktop 发布](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)。

## <a name="see-also"></a>另请参阅
* [使用 Data Lake Analytics 分析 Data Lake Store 中的数据](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


