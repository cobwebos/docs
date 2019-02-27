---
title: 在 Power BI 仪表板中可视化 Azure IoT Central 数据 | Microsoft Docs
description: 使用 Azure IoT Central 的 Power BI 解决方案来可视化和分析 IoT Central 数据。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328089"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据

*本主题适用于管理员。*

![Power BI 解决方案管道](media/howto-connect-powerbi/iot-continuous-data-export.png)

使用 Azure IoT Central 的 Power BI 解决方案来创建功能强大的 Power BI 仪表板，以监视 IoT 设备的性能。 在 Power BI 仪表板中，可以：
- 跟踪设备在不同时间段发送的数据量
- 比较遥测、状态和事件之间的数据量
- 识别报告大量度量值的设备
- 观察设备测量值的历史趋势
- 识别发送大量关键事件的有问题设备

此解决方案会设置一个管道，用于通过[连续数据导出](howto-export-data.md)将数据引入 Azure Blob 存储帐户。 此数据流入 Azure Functions、Azure 数据工厂和 Azure SQL 数据库以处理和转换数据。 输出可以在 Power BI 报告中得到可视化和分析，并可将该报告下载为 PBIX 文件。 所有这些资源在 Azure 订阅中创建，因此，你可以根据需求自定义每个组件。

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>从 Microsoft AppSource 中获取 [Azure IoT Central 的 Power BI 解决方案](https://aka.ms/iotcentralpowerbisolutiontemplate)。

## <a name="prerequisites"></a>先决条件
设置此解决方案需要以下各项：
- Azure 订阅的访问权限
- 使用 IoT Central 应用中的[连续数据导出](howto-export-data.md)来导出数据。 我们建议启用度量、设备和设备模板流，以充分利用 Power BI 仪表板。
- Power BI Desktop（最新版本）
- Power BI Pro（如果想要与其他人共享仪表板）

## <a name="reports"></a>报告

会自动生成两份报表。 

第一份报表显示设备报告的度量值历史视图，详细划分不同类型的度量值，并详细列出发送了最多度量值的设备。

![Power BI 报表页 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

第二份报表更深入地分析事件，并显示报告的错误和警告的历史视图。 此外，它还显示哪些设备到目前为止报告了最多的事件，并专门显示错误事件和警告事件。

![Power BI 报表页 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>体系结构
在 Azure 门户中可以访问所有已创建的资源。 所有内容都应该在一个资源组内。

![资源组的 Azure 门户视图](media/howto-connect-powerbi/azure-deployment.PNG)

下面描述了每个资源的细节及其使用途径。

### <a name="azure-functions"></a>Azure Functions
每次有新的文件写入到 Blob 存储都会触发 Azure Functions 应用。 这些函数提取每个度量、设备和设备模板文件中的字段，并填充 Azure 数据工厂要使用的几个中间 SQL 表。

### <a name="azure-data-factory"></a>Azure 数据工厂
Azure 数据工厂作为链接服务连接到 SQL 数据库。 它运行存储过程活动，处理数据并将其存储在分析表中。

### <a name="azure-sql-database"></a>Azure SQL 数据库
将自动创建这些表以填充默认报表。 探索 Power BI 中的这些架构，且可以生成自己对此数据的可视化效果。

| 表名称 |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>估计成本

以下是涉及 Azure 成本（Azure Functions、数据工厂、Azure SQL）的估算值。 所有价格以美元为单位。 请记住，价格因区域而异，因此你应该始终查询单项服务的最新价格以知悉实际价格。
以下是模板中的默认设置（设置完成后可以任意修改）：

- Azure Functions：应用服务计划 S1，74.40 美元/月
- Azure SQL S1，约 30 美元/月

我们鼓励你了解各种定价选项并调整以满足你的需求。

## <a name="resources"></a>资源

访问 AppSource 以获取 [Azure IoT Central 的 Power BI 解决方案](https://aka.ms/iotcentralpowerbisolutiontemplate)。

## <a name="next-steps"></a>后续步骤

了解如何在 Power BI 中可视化数据后，建议接下来继续学习：

> [!div class="nextstepaction"]
> [如何管理设备](howto-manage-devices.md)