---
title: 用于了解车辆运行状况和行驶习惯的 Power BI 仪表板 - Azure | Microsoft Docs
description: 通过 Cortana Intelligence 的功能获得对车辆运行状况和驾驶习惯的实时和预测性深入了解。
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 085ce90311d4d89b365f7fe51a95c00c1a734196
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>车辆遥测分析解决方案模板 Power BI 仪表板设置说明
此菜单链接至此操作手册的此章节。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

车辆遥测分析解决方案展示汽车经销商、汽车制造商和保险公司如何使用 Cortana Intelligence 的功能。 他们可以获得对车辆运行状况和驾驶习惯的实时和预测性见解，以改善客户体验、提高研发水平以及促进营销活动。 这些分布说明介绍了在订阅中部署解决方案后，如何配置 Power BI 报表和仪表板。 

## <a name="prerequisites"></a>先决条件
* 部署[车辆遥测分析](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90)解决方案。 
* [安装 Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)。
* 获取 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)。 如果还没有 Azure 订阅，请开始使用 Azure 免费订阅。
* 打开一个 Power BI 帐户。

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence 套件组件
作为车辆遥测分析解决方案模板的一部分，以下 Cortana Intelligence 服务部署在订阅中：

* **Azure 事件中心**：将数百万车辆遥测事件引入 Azure。
* **Azure 流分析**：提供对车辆运行状况的实时深入了解，并将数据持续存储到长期存储中，以进行更丰富的批处理分析。
* **Azure 机器学习**：实时检测异常，并使用批处理提供预测性见解。
* **Azure HDInsight**：大规模转换数据。
* **Azure 数据工厂**：处理批处理管道的业务流程、计划、资源管理和监视。

**Power BI** 为该解决方案提供了丰富的仪表板，用于数据和预测分析可视化。 

该解决方案使用两个不同的数据源：

* 模拟车辆信号和诊断数据集
* 车辆目录

车辆远程信息处理模拟器是此解决方案的一部分。 它在给定的时间点根据车辆的状态和驾驶模式发出对应的诊断信息和信号。 

车辆目录是将 VIN 映射到模型的参考数据集。

## <a name="power-bi-dashboard-preparation"></a>Power BI 仪表板准备
### <a name="set-up-the-power-bi-real-time-dashboard"></a>设置 Power BI 实时仪表板

#### <a name="start-the-real-time-dashboard-application"></a>启动实时仪表板应用程序
部署完成后，请按照手动操作说明操作。

1. 下载实时仪表板应用程序 RealtimeDashboardApp.zip，然后将其解压缩。

2.  在已解压缩的文件夹中，打开应用配置文件 RealtimeDashboardApp.exe.config。使用手动操作说明中的值替换事件中心、Azure Blob 存储和 Azure 机器学习服务连接中的应用设置。 保存所做更改。

3. 运行应用程序 RealtimeDashboardApp.exe。 在登录窗口中，输入有效的 Power BI 凭据。 

   ![Power BI 登录窗口](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. 选择“接受”。 应用开始运行。

   ![Power BI 仪表板权限](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. 登录 Power BI 网站，并创建实时仪表板。

现已准备好配置 Power BI 仪表板。  

### <a name="configure-power-bi-reports"></a>配置 Power BI 报表
实时报表和仪表板需要大约 30 到 45 分钟才能完成。 

1. 浏览到 [Power BI](http://powerbi.com) 网页并登录。

    ![Power BI 登录页](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. 在 Power BI 中生成新的数据集。 选择“ConnectedCarsRealtime”数据集。

    ![ConnectedCarsRealtime 数据集](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. 要保存空白报表，请按 Ctrl+S。

    ![空白报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. 输入报表名称“车辆遥测分析实时 - 报表”。

    ![报表名称](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>实时报表
此解决方案中有三种实时报表：

* 运行中的车辆
* 需要维护的车辆
* 车辆运行状况统计信息

可以配置所有三种报表，也可以在任意阶段结束后停止。 然后可以继续进行下一节（如何配置批处理报表）。 我们建议创建所有报表，以可视化解决方案实时路径的完整见解。  

### <a name="vehicles-in-operation-report"></a>“运行中的车辆”报表
1. 双击“第 1 页”，将其重命名为“运行中的车辆”。

    ![运行中的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. 在“字段”选项卡上，选择“vin”。 在“可视化”选项卡上，选择“卡”可视化。  

    如下图所示创建“卡”可视化。

    ![选择“vin”](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. 选择空白区域，添加新的可视化。  

4. 在“字段”选项卡上，选择“市/县”和“vin”。 在“可视化”选项卡上，选择“地图”可视化。 将“vin”拖到“值”区域。 将“市/县”拖到“图例”区域。 

    ![卡可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. 在“可视化”选项卡上，选择“格式”部分。 选择“标题”，将“文本”更改为“按市/县划分的运行中的车辆”。

    ![按市/县划分的运行中的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    最终的可视化效果如以下示例所示：

    ![最终的可视化效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. 选择空白区域，添加新的可视化。  

7. 在“字段”选项卡上，选择“市/县”和“vin”。 在“可视化”选项卡上，选择“簇状柱形图”可视化。 将“市/县”拖到“轴”区域。 将“vin”拖到“值”区域。

8. 通过“vin 的计数”对图表排序。

    ![vin 的计数](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. 将图表“标题”更改为“按市/县划分的运行中的车辆”。 

10. 选择“格式”部分，然后选择“数据颜色”。 将“显示全部”更改为“开启”。

    ![数据颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. 通过选择颜色符号，更改单个市/县的颜色。

    ![颜色更改](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. 选择空白区域，添加新的可视化。  

13. 在“可视化”选项卡上，选择“簇状柱形图”可视化。 在“字段”选项卡上，将“市/县”拖到“轴”区域。 将“模型”拖到“图例”区域。 将“vin”拖到“值”区域。

    ![簇状柱形图](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    图表如以下映像所示：

    ![渲染](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. 重新排列所有可视化效果，以便页如以下示例所示：

    ![带可视化的仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

已成功配置“运行中的车辆”报表。 可以创建下一个实时报表，也可以在此处停止并配置仪表板。 

### <a name="vehicles-requiring-maintenance-report"></a>“需要维护的车辆”报表

1. 选择“![添加](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)”以添加新报表。 将其重命名为“需要维护的车辆”。

    ![需要维护的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. 在“字段”选项卡上，选择“vin”。 在“可视化”选项卡上，选择“卡”可视化。

    ![Vin 卡可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    数据集包含一个名为“维修标签”的字段。 此字段的值为“0”或“1”。 该值由预配为解决方案的一部分的机器学习模型设置。 它与实时路径集成。 值“1”表示车辆需要维护。 

3. 若要添加“页面级筛选器”以显示需要维护的车辆的数据，请执行以下操作： 

   a. 将“维修标签”字段拖到“页面级筛选器”。
  
      ![页面级筛选器](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. 在“页面级筛选器维修标签”的底部，选择“基本筛选”。

      ![基本筛选](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. 将筛选器值设置为“1”。

      ![筛选器值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. 选择空白区域，添加新的可视化。  

5. 在“可视化”选项卡上，选择“簇状柱形图”可视化。 

    ![Vin 卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![簇状柱形图](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. 在“字段”选项卡上，将“模型”拖到“轴”区域。 将“vin”拖到“值”区域。 然后按照“vin 的计数”对可视化排序。 将图表“标题”更改为“按模型划分的需要维护的车辆”。 

7. 在“可视化”选项卡的“字段” ![Fields-image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 部分中，将“vin”拖到“颜色饱和度”。

    ![颜色饱和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. 在“格式”部分，更改可视化中的“数据颜色”： 

    a. 将颜色“最小值”更改为“F2C812”。

    b. 将颜色“最大值”更改为“FF6300”。

    ![新数据颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    新可视化颜色如以下示例所示：

    ![新可视化颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. 选择空白区域，添加新的可视化。  

10. 在“可视化”选项卡上，选择“簇状柱形图”。 将“vin”拖到“值”区域。 将“市/县”拖到“轴”区域。 通过“vin 的计数”对图表排序。 将图表“标题”更改为“按市/县划分的需要维护的车辆”。

    ![按市/县划分的需要维护的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. 选择空白区域，添加新的可视化。  

12. 在“可视化”选项卡上，选择“多行卡”可视化。 将“模型”和“vin”拖到“字段”区域。

    ![多行卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. 重新排列所有可视化效果，以便最终报表如以下示例所示： 

    ![已重新排列的最终报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

已成功配置“需要维护的车辆”实时报表。 可以创建下一个实时报表，也可以在此处停止并配置仪表板。 

### <a name="vehicle-health-statistics-report"></a>“车辆运行状况统计信息”报表

1. 选择“![添加](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)”以添加新报表。 将其重命名为“车辆运行状况统计信息”。 

2. 在“可视化”选项卡上，选择“仪表”可视化。 将“速度”拖到“值”、“最小值”和“最大值”区域。

   ![车辆运行状况统计信息](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. 在“值”区域中，将“速度”的默认聚合更改为“平均值”。

4. 在“最小值”区域中，将“速度”的默认聚合更改为“最小值”。

5. 在“最大值”区域中，将“速度”的默认聚合更改为“最大值”。

   ![速度值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. 将“仪表标题”重命名为“速度平均值”。

   ![仪表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. 选择空白区域，添加新的可视化。  

    同样，为“引擎机油平均值”、“燃油平均值”和“引擎温度平均值”添加“仪表”。  

8. 采用“速度平均值”仪表中的上述步骤，更改每个仪表中字段的默认聚合。

    ![附加仪表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. 选择空白区域，添加新的可视化。

10. 在“可视化”选项卡上，选择“折线和簇状柱形图”可视化。 将“市/县”拖到“共享轴”。 将“胎压”、“引擎机油”和“速度”拖到“列值”区域。 将它们的聚合类型更改为“平均值”。 

11. 将“引擎温度”拖到“行值”区域。 将聚合类型更改为“平均值”。 

    ![列和行值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. 将图表“标题”更改为“速度、胎压、引擎机油和引擎温度的平均值”。  

    ![行和簇状柱形图](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. 选择空白区域，添加新的可视化。

14. 在“可视化”选项卡上，选择“树状图”可视化。 将“模型”拖到“组”区域。 将“维修率”拖到“值”区域。

15. 将图表“标题”更改为“需要维护的车辆模型”。

    ![树状图标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. 选择空白区域，添加新的可视化。

17. 在“可视化”选项卡上，选择“100% 堆叠条形图”可视化。 将“市/县”拖到“轴”区域。 将“维修率”和“召回率”拖到“值”区域。

    ![轴和值区域](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. 在“格式”部分，选择“数据颜色”。 将“维修率”颜色设置为值“F2C80F”。

19. 将图表“标题”更改为“按市/县划分的车辆维修率和召回率”。

    ![100% 堆叠条形图标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. 选择空白区域，添加新的可视化。

21. 在“可视化”选项卡上，选择“区域图表”可视化。 将“模型”拖到“轴”区域。 将“引擎机油”、“胎压”、“速度”和“维修率”拖到“值”区域。 将它们的聚合类型更改为“平均值”。 

    ![聚合类型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. 将图表“标题”更改为“按模型计算的引擎机油、胎压、速度和维修率的平均值”。

    ![分区图标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. 选择空白区域，添加新的可视化。

24. 在“可视化”选项卡上，选择“散布图”可视化。 将“模型”拖到“详细信息”和“图例”区域。 将“燃油”拖到“X 轴”区域。 将聚合更改为“平均值”。 将“引擎温度”拖到“Y 轴”区域。 将聚合更改为“平均值”。 将“vin”拖到“大小”区域。

    ![详细信息、图例、轴和大小区域](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. 将图表“标题”更改为“按模型划分的燃油平均值、引擎温度平均值和 vin 的计数”。

    ![散布图标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    最终报表如以下示例所示：

    ![最终报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>从报表固定可视化至实时仪表板
1. 选择“仪表板”旁边的加号创建一个空白仪表板。 输入名称“车辆遥测分析仪表板”。

    ![仪表板加号](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. 将来自之前报表的可视化固定至仪表板。 

    ![仪表板固定符号](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    全部报表固定到仪表板时，应如以下示例所示。 如果未创建全部报表，则仪表板看起来会有所不同。 

    ![带报表的仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

已成功创建实时仪表板。 当继续执行 CarEventGenerator.exe 和 RealtimeDashboardApp.exe 时，可在仪表板上看到实时更新。 完成以下步骤大约需要 10 到 15 分钟。

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>设置 Power BI 批处理仪表板
> [!NOTE]
> 对于端对端批处理管道而言，要完成执行并处理一年内生成的数据需要大约两个小时（从成功完成部署算起）。 请等待处理完成，然后继续执行后续步骤：
> 
> 

### <a name="download-the-power-bi-designer-file"></a>下载 Power BI 设计器文件

1. 预配置的 Power BI 设计器文件属于部署手动操作说明的一部分。 查找“2. 设置 PowerBI 批处理仪表板。”

2. 在此处下载用于批处理仪表板的名为“ConnectedCarsPbiReport.pbix”的 Power BI 模板。

3. 保存到本地。

### <a name="configure-power-bi-reports"></a>配置 Power BI 报表

1. 使用 Power BI Desktop 打开设计器文件“ConnectedCarsPbiReport.pbix”。 如果没有，请从 [Power BI Desktop 安装](http://www.microsoft.com/download/details.aspx?id=45331)网站安装 Power BI Desktop。

2. 选择“编辑查询”。

    ![编辑查询](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. 双击“源”。

    ![Source](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. 使用作为部署的一部分提供的 Azure SQL Server 来更新服务器连接字符串。 查看 Azure SQL 数据库中的手动操作说明：

    * Server：somethingsrv.database.windows.net
    * Database：connectedcar
    * Username：username
    * 密码：可以从 Azure 门户管理 SQL Server 密码。

5. 将“数据库”保留为 connectedcar。

    ![数据库](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. 选择“确定”。

7. 默认选择“Windows 凭据”选项。 通过选择右侧的“数据库”选项卡，将其更改为“数据库凭据”。

8. 输入在部署设置期间指定的 Azure SQL 数据库的“用户名”和“密码”。

    ![数据库凭据](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. 选择“连接”。

10. 对右侧窗格中显示的剩下三个查询重复前面的步骤。 然后更新数据源连接详细信息。

11. 选择“关闭并加载”。 Power BI Desktop 文件数据集连接到 SQL 数据库表。

12. 选择“关闭”关闭 Power BI Desktop 文件。

    ![关闭](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. 选择“保存”，保存更改。 

现在已配置完与解决方案中批处理路径相对应的所有报表。 

## <a name="upload-to-powerbicom"></a>上传到 powerbi.com
1. 转到 [Power BI Web 门户](http://powerbi.com)并登录。

2. 选择“获取数据”。

3. 上传 Power BI Desktop 文件。 选择“获取数据” > “文件获取” > “本地文件”。

4. 转到“ConnectedCarsPbiReport.pbix”。

5. 文件完成上传后，返回到 Power BI 工作空间。 将创建数据集、报表和空白仪表板。  

6. 将图表固定到 Power BI 中名为“车辆遥测分析仪表板”的新仪表板上。 选择前面已创建的空白仪表板，然后转到“报表”部分。 选择新上传的报表。  

    ![新 Power BI 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    报表有六页：

    第 1 页：车辆密度  
    第 2 页：实时车辆运行状况  
    第 3 页：过激驾驶的车辆   
    第 4 页：召回的车辆  
    第 5 页：燃油高效驾驶的车辆  
    第 6 页：Contoso Motors 徽标  

    ![六页 Power BI 报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. 从第 3 页起，固定以下内容：  

    a. vin 的计数  

   ![第 3 页 vin 的计数](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. 按模型划分的过激驾驶车辆 - 瀑布图 

   ![第 3 页图表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. 从第 5 页起，固定以下内容： 

    a. vin 的计数

   ![第 5 页图表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. 按模型划分的燃油高效车辆：簇状柱形图

   ![第 5 页图表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. 从第 4 页起，固定以下内容：  

    a. vin 的计数 

   ![第 4 页图表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. 按市/县划分的召回的汽车、模型：树状图

   ![第 4 页图表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. 从第 6 页起，固定以下内容：  

    * Contoso Motors 徽标

    ![Contoso Motors 徽标](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>组织仪表板  

1. 请转到仪表板。

2. 将鼠标悬停在每个图表上。 根据以下已完成的仪表板示例提供的命名，对每个图表重新命名：

   ![仪表板组织](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. 移动图表，使其如以下仪表板示例所示：

    ![经过重新排列的仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. 创建了本文档中提及到的所有报表之后，最终完成的仪表板应如以下示例所示： 

   ![最终的仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

你已成功创建报表和仪表板，可获得对车辆运行状况和驾驶习惯的实时、预测性和批处理的见解。  
