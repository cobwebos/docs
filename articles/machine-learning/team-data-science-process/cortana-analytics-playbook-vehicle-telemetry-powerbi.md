---
title: "用于了解车辆运行状况和行驶习惯的 Power BI 仪表板 - Azure | Microsoft Docs"
description: "通过 Cortana Intelligence 的功能获得对车辆运行状况和驾驶习惯的实时和预测性深入了解。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>车辆遥测分析解决方案模板 Power BI 仪表板设置说明
此“菜单”链接至此操作手册的此章节。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

车辆遥测分析解决方案展示了汽车经销商、汽车制造商和保险公司如何利用 Cortana Intelligence 的能力，获得车辆运行状况和驾驶习惯的实时和预测性深入了解，从而推动客户体验、研发和营销活动领域的改进。 此文档包含有关在订阅中部署解决方案后，如何配置 Power BI 报表和仪表板的分步说明。 

## <a name="prerequisites"></a>先决条件
1. 部署[遥测分析](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90)解决方案  
2. [Install Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)（安装 Microsoft Power BI Desktop）
3. 一个 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)。 如果还没有 Azure 订阅，请开始使用 Azure 免费订阅
4. Microsoft Power BI 帐户

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence 套件组件
作为车辆遥测分析解决方案模板的一部分，以下 Cortana Intelligence 服务部署在订阅中。

* **事件中心**，用于将数百万车辆遥测事件引入 Azure。
* **流分析**，用于获得对车辆运行状况的实时深入了解，并将数据持续存储到长期存储中，以进行更丰富的批处理分析。
* **机器学习**，用于实时和批处理的异常情况检测，以获得预测性的深入了解。
* **HDInsight**，用于大规模转换数据
* **数据工厂**用于处理批处理管道的业务流程、计划、资源管理和监视。

**Power BI** 为该解决方案提供了丰富的仪表板，用于实时数据和预测分析可视化。 

该解决方案使用两个不同的数据源：**模拟车辆信号和诊断数据集**和**车辆目录**。

车辆远程信息处理模拟器是此解决方案的一部分。 它发出对应于车辆的状态和在给定时间点的驾驶模式的诊断信息和信号。 

车辆目录是包含 VIN 到模型映射的引用数据集

## <a name="power-bi-dashboard-preparation"></a>Power BI 仪表板准备
### <a name="setup-power-bi-real-time-dashboard"></a>设置 Power BI 实时仪表板

**启动实时仪表板应用程序** 完成部署后，应按照手动操作说明操作

* 下载实时仪表板应用程序 RealtimeDashboardApp.zip，然后将其解压缩。
*  在解压缩文件夹中，打开应用配置文件“RealtimeDashboardApp.exe.config”，将 Eventhub、Blob 存储和 ML 服务连接的 appSettings 替换为手动操作说明中的值，然后保存更改。
* 运行应用程序 RealtimeDashboardApp.exe。 弹出登录窗口后，提供有效的 PowerBI 凭据，并单击“接受”按钮。 此时该应用将开始运行。

   ![登录到 Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Power BI 仪表板权限](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* 登录到 PowerBI 网站，并创建实时仪表板。

现在，已准备好配置具有丰富可视化的 Power BI 仪表板，以获得车辆运行状况和驾驶习惯的实时和预测性的深入了解。 创建所有报表和配置仪表板大约需要 45 分钟到 1 小时。 

### <a name="configure-power-bi-reports"></a>配置 Power BI 报表
实时报表和仪表板需要大约 30 - 45 分钟才能完成。 浏览 [ http://powerbi.com ](http://powerbi.com) 并登录。

![登录到 Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

在 Power BI 中生成新的数据集。 单击“ConnectedCarsRealtime”数据集。

![选择连接的汽车实时数据集](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

使用 **Ctrl + s**  保存空白报表。

![保存空白报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

提供报表名称，车辆遥测分析实时 - 报表。

![提供报表名称](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>实时报表
此解决方案中有三种实时报表：

1. 运行中的车辆
2. 需要维护的车辆
3. 车辆运行状况统计信息

可以选择配置全部三个实时报表，或在任何阶段后停止，继续进行配置批处理报表的下一部分。 我们建议创建全部的三个报告，以可视化解决方案实时路径的完整深入了解。  

### <a name="1-vehicles-in-operation"></a>1.运行中的车辆
双击“第 1 页”，将其重命名为“运行中的车辆”  
    ![已连接的汽车 - 运行中的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

从“字段”中选择“vin”字段，并选择可视化类型为“卡”。  

如图所示创建卡可视化。  
    ![已连接的汽车-选择 vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

单击空白区域，添加新的可视化。  

从字段中选择“市/县”vin。 将可视化更改为“映射”。 在值区域中拖动“vin”。 从字段中将“市/县”拖动至“图例”区域。   
    ![已连接的汽车 - 卡可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

从“可视化”中选择“格式”，单击“标题”并将“文本”更改至“按市/县划分的运行中的车辆”。  
    ![已连接的汽车 - 按市/县划分的运行中的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

最终的可视化如图所示。    
    ![已连接的汽车 - 最终可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

单击空白区域，添加新的可视化。  

选择“市/县”和“vin”，将可视化类型更改至“簇状柱形图”。 确保“市/县”位于“轴区域”，“vin”位于“值区域”  

通过“vin 计数”将图表排序  
    ![已连接的汽车 - vin 计数](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

将图表中的“标题”更改为“按市/县划分的运行中的车辆”  

单击“格式”部分，选择“数据颜色”，并单击“开”来“全部显示”  
    ![已连接的汽车 - 显示所有数据颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

通过单击颜色图标更改个别市/县的颜色。  
    ![已连接的汽车 - 更改颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

单击空白区域，添加新的可视化。  

从可视化中选择“簇状柱形图”可视化，拖动“轴区域”的“市/县”字段、“图例”区域的“模型”以及“值区域”中的“vin”。  
    ![已连接的汽车 - 簇状柱形图](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![已连接的汽车 - 呈现](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

在此页面上重新排列所有可视化，如图所示。  
    ![已连接的汽车 - 可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

已成功配置“运行中的车辆”实时报表。 可以继续创建下一个实时报表，或在此处停止并配置仪表板。 

### <a name="2-vehicles-requiring-maintenance"></a>2.需要维护的车辆
点击![添加](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)以添加新报表，将其重命名为**“需要维护的车辆”**

![已连接的汽车 - 需要维护的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

选择“vin”字段并将可视化类型更改为“卡”。  
    ![已连接的汽车 - vin 卡可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

在数据集中有一个名为“MaintenanceLabel”的字段。 该字段的值为“0”或“1”。 Azure 机器学习模型将其设置为解决方案的一部分，并与实时路径聚合。 值“1”表示车辆需要维护。 

若要为需要维护的车辆添加“页面级”筛选器，以显示车辆数据： 

1. 将“MaintenanceLabel”字段拖动到“页面级筛选器”。  
   ![已连接的汽车 - 页面级筛选器](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. 单击位于 MaintenanceLabel 页面级筛选器底部的“基本筛选”菜单。  
   ![已连接的汽车 - 基本筛选](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. 将它的筛选值设置为 **“1”**    
   ![已连接的汽车 - 筛选值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

单击空白区域，添加新的可视化。  

从可视化中选择“簇状柱形图”  
![已连接的汽车 - vind 卡可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![已连接的汽车 - 簇状柱形图](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

将字段“模型”拖动至“轴区域”，将“vin”拖动至“值区域”。 然后按照“vin 计数”对视觉对象排序。  将图表的“标题”更改为“车辆需要通过模型进行维护”  

将“vin”字段拖动至位于“可视化”选项卡中“字段”“字段”![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png)部分的“色彩饱和度”  
![已连接的汽车 - 色彩饱和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

从可视化中的“格式”部分更改“色彩饱和度”  
将颜色最小值更改为：**F2C812**  
将颜色最大值更改为：**FF6300**  
![已连接的汽车 - 色彩更改](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![已连接的汽车 - 新可视化颜色](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

单击空白区域，添加新的可视化。  

从可视化中选择“簇状柱形图”，将“vin”字段拖动到“值区域”，将“市/县”字段拖动到“轴区域”。 通过“vin 计数”对图表排序。 将图表的“标题”更改为“车辆需要通过市/县进行维护”   
![已连接的汽车 - 需要通过市/县维护的车辆](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

单击空白区域，添加新的可视化。  

从可视化中选择“多行卡”，将“模型”和“vin”拖动到“字段”区域。  
![已连接的汽车 - 多行卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

重新排列所有的可视化，其最终报表如下：  
![已连接的汽车 - 多行卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

已成功配置“需要维护的车辆”实时报表。 可以继续创建下一个实时报表，或在此处停止并配置仪表板。 

### <a name="3-vehicles-health-statistics"></a>3.车辆运行状况统计信息
单击“添加”![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)来添加新报表，将其重命名为**“车辆运行状况统计信息”**  

从可视化中选择“仪表”可视化，然后将“速度”字段拖动至“值、最小值、最大值”区域。  
![已连接的汽车 - 多行卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

将“值区域”中默认聚合的“速度”更改为“平均值” 

将“最小值区域”中默认聚合的“速度”更改为“最小值”

将“最大值区域”中默认聚合的“速度”更改为“最大值”

![已连接的汽车 - 多行卡](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

将“仪表标题”重命名为“平均速度” 

![已连接的汽车 - 仪表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

单击空白区域，添加新的可视化。  

同样，为“平均引擎燃油”、“平均燃油”、“平均引擎温度”添加“仪表”。  

根据“平均速度”仪表中的上述步骤，更改每个仪表中字段的默认聚合。

![已连接的汽车 - 仪表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

单击空白区域，添加新的可视化。

从可视化中选择“折线和簇状柱形图”，然后将“市/县”字段拖动至“共享轴”，将“速度”、“轮胎压力和引擎燃油字段”拖动至“列值”区域，并将它们的聚合类型更改为“平均”。 

将“engineTemperature”字段拖动至“行值”区域，并将聚合类型更改为“平均”。 

![已连接的汽车 - 可视化字段](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

将图表的“标题”更改为“平均速度、车胎压力、引擎燃油及引擎温度”。  

![已连接的汽车 - 可视化字段](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

单击空白区域，添加新的可视化。

从可视化中选择“树状图”可视化，将“模型”字段拖动至“组”区域，然后拖动“MaintenanceProbability”字段值“值区域”。

将图表的“标题”更改为“车辆模型需要维护”。

![已连接的汽车 - 更改图表标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

单击空白区域，添加新的可视化。

从可视化中选择“100%堆栈条形图”，将“市/县”字段拖动至“轴区域”，将“MaintenanceProbability”、“RecallProbability”字段拖动至“值区域”。

![已连接的汽车 - 添加新可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

单击“格式”，选择“数据颜色”，然后将“MaintenanceProbability”颜色的值设置为“F2C80F”。

将图表的“标题”更改为**“按市/县划分的车辆维护和召回的概率”**。

![已连接的汽车 - 添加新可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

单击空白区域，添加新的可视化。

从可视化中选择“区域图表”，将“模型”字段拖动至“轴区域”，然后将“引擎燃油、车胎压力、速度和维护概率”字段拖动至“值区域”。 将它们的聚合类型更改为“平均”。 

![已连接的汽车 - 更改聚合类型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

将图表的标题更改为“按模型计算的平均引擎燃油、车胎压力、速度和维护概率”。

![已连接的汽车 - 更改图表标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

单击空白区域，添加新的可视化：

1. 可视化中选择“散点图”可视化。
2. 将“模型”字段拖动至“详细信息”和“图例”区域。
3. 将“燃油”字段拖动至“X 轴”区域，将聚合更改为“平均”。
4. 将“引擎温度”字段拖动至“Y 轴”区域，将聚合更改为“平均”
5. 将“vin”拖动至“大小”区域。

![已连接的汽车 - 添加新可视化](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

将图表的“标题”更改为“按模型计算的平均燃油、车引擎温度”。

![已连接的汽车 - 更改图表标题](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最终报表将如下所示。

![已连接的汽车 - 最终报表](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>从报表固定可视化至实时仪表板
通过单击仪表板旁边的加号图标创建一个空白仪表板。 可以将其命名为“车辆遥测分析仪表板”

![已连接的汽车 - 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

将来自上述报表的可视化固定至仪表板。 

![已连接的汽车 - 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

当全部三个报表都被创建，且相应的视觉化已被固定至仪表板时，仪表板应该如下所示。 如果尚未创建全部的报表，则仪表板看起来会有所不同。 

![已连接的汽车 - 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

祝贺你！ 已成功创建实时仪表板。 当继续执行 CarEventGenerator.exe 和 RealtimeDashboardApp.exe 时，应能在仪表板上看到实时更新。 完成以下步骤大约需要 10 到 15 分钟。

## <a name="setup-power-bi-batch-processing-dashboard"></a>设置 Power BI 批处理仪表板
> [!NOTE]
> 对于端对端批处理管道而言，要完成执行并处理一年内生成的数据需要大约两个小时（从成功完成部署算起）。 所以请等待处理完成，并继续下一步。 
> 
> 

**下载 Power BI 设计器文件**

* 预配置的 Power BI 设计器文件属于部署手动操作说明的一部分
* 查找 2。 设置 PowerBI 批处理仪表板 可以在此处为批处理仪表板下载名为“ConnectedCarsPbiReport.pbix”的 PowerBI 模板。
* 本地保存

**配置 Power BI 报表**

* 使用 Power BI Desktop 打开名为“ConnectedCarsPbiReport.pbix”的设计器文件。 如果没有，请从 [Power BI Desktop 安装](http://www.microsoft.com/download/details.aspx?id=45331)来安装 Power BI Desktop。 
* 单击“编辑查询”。

![编辑 Power BI 查询](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* 双击“源”。

![设置 Power BI 源](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* 使用作为部署的一部分设置的 Azure SQL Server 更新服务器连接字符串。  查看如下所示的手动操作说明 

    4. Azure SQL 数据库
    
    * Server：somethingsrv.database.windows.net
    * Database：connectedcar
    * Username：username
    * Password：可以通过 Azure 门户管理 SQL Server 的密码

* 将“数据库”保留为 connectedcar。

![设置 Power BI 数据库](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* 单击 **“确定”**。
* 会看到默认选择“Windows 凭据”选项卡，通过单击右边“数据库”选项卡上的“数据库凭据”对其进行更改。
* 提供 Azure SQL 数据库的“用户名”和“密码”，它们在部署设置的过程中被指定。

![提供数据库凭据](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* 单击“连接”
* 对右侧窗格中剩下的其余三个查询重复上述步骤，并更新数据源连接详细信息。
* 单击“关闭并加载”。 Power BI Desktop 文件数据集连接到 SQL Azure 数据库表。
* “关闭” Power BI Desktop 文件。

![关闭 Power BI Desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* 单击“保存”按钮保存所做的更改。 

现在已完成全部与解决方案中批处理路径相对于的报表的配置。 

## <a name="upload-to-powerbicom"></a>上传到 powerbi.com
1. 导航至 Power BI Web 门户（地址是 http://powerbi.com）并登录。
2. 单击“获取数据”  
3. 上传 Power BI Desktop 文件。  
4. 若要上传，请单击“获取数据”->“获取文件”->“本地文件”  
5. 导航到“ConnectedCarsPbiReport.pbix”  
6. 一旦文件完成上传，你将被导航回你的 Power BI 工作空间。  

将创建数据集、报表和空白仪表板。  

将图表固定到“Power BI”中名为“车辆遥测分析仪表板”的新仪表板上。 单击上面创建的空白仪表板，然后导航至“报表”部分单击新上传的报表。  

![Vehicle Telemetry Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**请注意报表共有 6 页**  
第 1 页：车辆密度  
第 2 页：实时车辆运行状况  
第 3 页：过激驾驶的车辆   
第 4 页：召回的车辆  
第 5 页：燃油高效驾驶的车辆  
第 6 页：Contoso 徽标  

![Connected Cars Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**从第 3 页起**，固定以下：  

1. vin 的计数  
   ![Connected Cars Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. 按模型的过激驾驶车辆 - 瀑布图  
   ![车辆遥测 - 固定图表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**从第 5 页起**，固定以下： 

1. vin 的计数    
   ![车辆遥测 - 固定图表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. 按模型的燃油高效车辆：簇状柱形图  
   ![车辆遥测 - 固定图表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**从第 4 页起**，固定以下：  

1. vin 的计数  
   ![车辆遥测 - 固定图表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. 按市/县召回的汽车，模型：树状图  
   ![车辆遥测 - 固定图表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**从第 6 页起**，固定以下：  

1. Contoso Motors 徽标  
   ![车辆遥测 - 固定图表 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**组织仪表板**  

1. 导航至仪表板
2. 将鼠标悬停在每个图表上，并根据下面整个仪表板图像中提供的命名对其重命名。 同时也移动图表，使其看起来像下面的仪表板。  
   ![车辆遥测 - 组织仪表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Vehicle Telemetry Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. 如果已创建本文档中提及的所有报表，则最终完成的仪表板应如下图所示。 

![车辆遥测 - 组织仪表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

祝贺你！ 已成功创建报表和仪表板，以获得关于车辆运行状况和驾驶习惯的实时、预测性和批处理的深入了解。  
