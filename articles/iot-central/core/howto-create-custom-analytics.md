---
title: 通过自定义分析扩展 Azure IoT Central | Microsoft Docs
description: 解决方案开发人员将配置一个 IoT Central 应用程序来执行自定义分析和可视化。 此解决方案使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e04da10d71eed3706b87fc728a13927aeae82826
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84660126"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>使用 Azure Databricks 通过自定义分析扩展 Azure IoT Central

本操作指南向解决方案开发人员介绍如何使用自定义分析和可视化来扩展 IoT Central 应用程序。 本示例使用 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) 工作区来分析 IoT Central 遥测流并生成可视化效果（例如[框图](https://wikipedia.org/wiki/Box_plot)）。

本操作指南将介绍如何扩展 IoT Central，使其功能超越[内置分析工具](./howto-create-custom-analytics.md)的功能。

本操作指南的内容：

* 使用“连续数据导出”从 IoT Central 应用程序流式传输遥测数据。**
* 创建一个 Azure Databricks 环境用于分析和绘制设备遥测数据。

## <a name="prerequisites"></a>先决条件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上使用以下设置创建一个 IoT Central 应用程序：

| 设置 | Value |
| ------- | ----- |
| 定价计划 | Standard |
| 应用程序模板 | 店内分析 – 条件监视 |
| 应用程序名称 | 接受默认设置，或选择自己的名称 |
| URL | 接受默认设置，或选择自己的唯一 URL 前缀 |
| Directory | Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 区域 | 离你最近的区域 |

本文中的示例和屏幕截图使用“美国”区域。**** 选择靠近你的位置，并确保在同一区域中创建所有资源。

此应用程序模板包含两个可发送遥测数据的模拟恒温器设备。

### <a name="resource-group"></a>资源组

使用 [Azure 门户](https://portal.azure.com/#create/Microsoft.ResourceGroup)创建名为 **IoTCentralAnalysis** 的资源组，用于包含创建的其他资源。 在 IoT Central 应用程序所在的同一位置创建 Azure 资源。

### <a name="event-hubs-namespace"></a>事件中心命名空间

在 [Azure 门户中使用以下设置创建一个事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub)：

| 设置 | Value |
| ------- | ----- |
| 名称    | 选择命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| 位置 | 美国东部 |
| 吞吐量单位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作区

在 [Azure 门户中使用以下设置创建一个 Azure Databricks 服务](https://portal.azure.com/#create/Microsoft.Databricks)：

| 设置 | Value |
| ------- | ----- |
| 工作区名称    | 选择工作区名称 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| 位置 | 美国东部 |
| 定价层 | Standard |

创建所需的资源后，**IoTCentralAnalysis** 资源组将如以下屏幕截图所示：

![IoT Central 分析资源组](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可将 IoT Central 应用程序配置为向某个事件中心连续导出遥测数据。 在本部分，你将创建一个事件中心用于接收来自 IoT Central 应用程序的遥测数据。 该事件中心将遥测数据传送到流分析作业进行处理。

1. 在 Azure 门户中，导航到你的事件中心命名空间并选择“+ 事件中心”。****
1. 将事件中心命名为 **centralexport**，然后选择“创建”。****
1. 在命名空间中的事件中心列表内，选择“centralexport”。**** 然后选择“共享访问策略”。****
1. 选择“+ 添加”  。 创建包含 **Listen** 声明的名为 **Listen** 的策略。
1. 当该策略准备就绪时，请在列表中将其选中，然后复制“连接字符串 - 主密钥”值。****
1. 请记下此连接字符串，因为稍后在将 Databricks 笔记本配置为从事件中心读取数据时要用到它。

事件中心命名空间如以下屏幕截图所示：

![事件中心命名空间](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中配置导出

在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上，导航到从 Contoso 模板创建的 IoT Central 应用程序。 在本部分，你将配置应用程序，以将其模拟设备发来的遥测数据流式传输到事件中心。 若要配置导出：

1. 导航到“数据导出”页，依次选择“+ 新建”、“Azure 事件中心”。************
1. 使用以下设置配置导出，然后选择“保存”：****

    | 设置 | 值 |
    | ------- | ----- |
    | 显示名称 | 导出到事件中心 |
    | 已启用 | 开 |
    | 事件中心命名空间 | 事件中心命名空间的名称 |
    | 事件中心 | centralexport |
    | 度量 | 开 |
    | 设备 | 关闭 |
    | 设备模板 | 关闭 |

![数据导出配置](media/howto-create-custom-analytics/cde-configuration.png)

等到导出状态变为“正在运行”，然后继续。****

## <a name="configure-databricks-workspace"></a>配置 Databricks 工作区

在 Azure 门户中导航到你的 Azure Databricks 服务，然后选择“启动工作区”。**** 浏览器中会打开一个新的标签页，并将你登录到该工作区。

### <a name="create-a-cluster"></a>创建群集

在“Azure Databricks”页上的常用任务列表中，选择“新建群集”。********

使用下表中的信息创建群集：

| 设置 | Value |
| ------- | ----- |
| 群集名称 | centralanalysis |
| 群集模式 | Standard |
| Databricks 运行时版本 | 5.5 LTS（Scala 2.11、Spark 2.4.3） |
| Python 版本 | 3 |
| 启用自动缩放 | 否 |
| 在非活动几分钟后终止 | 30 |
| 辅助角色类型 | Standard_DS3_v2 |
| 工作节点 | 1 |
| 驱动程序类型 | 与辅助角色相同 |

创建群集可能需要几分钟时间，请等待群集创建完成，然后继续。

### <a name="install-libraries"></a>安装库

在“群集”页上，等到群集状态变为“正在运行”。********

以下步骤说明如何将示例所需的库导入群集：

1. 在“群集”页上，等到 **centralanalysis** 交互式群集的状态变为“正在运行”。********

1. 选择该群集，然后选择“库”选项卡。****

1. 在“库”选项卡上选择“安装新库”。********

1. 在“安装库”页上，选择“Maven”作为库源。********

1. 在“坐标”文本框中输入以下值：`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`****

1. 选择“安装”以在群集上安装该库。****

1. 库的状态现在为“已安装”：****

    ![已安装库](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>导入 Databricks 笔记本

使用以下步骤导入包含 Python 代码的 Databricks 笔记本，以分析和可视化 IoT Central 遥测数据：

1. 在 Databricks 环境中导航到“工作区”页。**** 选择帐户名旁边的下拉列表，然后选择“导入”。****

1. 选择 "从 URL 导入"，并输入以下地址： [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要导入笔记本，请选择“导入”。****

1. 选择“工作区”查看导入的笔记本：****

    ![导入的笔记本](media/howto-create-custom-analytics/import-notebook.png)

1. 编辑第一个 Python 单元中的代码，以添加前面保存的事件中心连接字符串：

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>运行分析

若要运行分析，必须将笔记本附加到群集：

1. 选择“已分离”，然后选择“centralanalysis”群集。********
1. 如果该群集未运行，请启动它。
1. 若要启动笔记本，请选择运行按钮。

最后一个单元中可能会出现错误。 如果是这样，请检查前面的单元是否正在运行，等待片刻时间以便将一些数据写入存储，然后再次运行最后一个单元。

### <a name="view-smoothed-data"></a>查看平滑化数据

在笔记本中，向下滚动到单元 14，查看按设备类型绘制的移动平均湿度图。 随着遥测流的传入，此绘图会不断更新：

![平滑化遥测图](media/howto-create-custom-analytics/telemetry-plot.png)

可以在笔记本中调整图表大小。

### <a name="view-box-plots"></a>查看框图

在笔记本中，向下滚动到单元 20 以查看[框图](https://en.wikipedia.org/wiki/Box_plot)。 框图基于静态数据，因此，若要对其进行更新，必须重新运行单元：

![框图](media/howto-create-custom-analytics/box-plots.png)

可以在笔记本中调整绘图大小。

## <a name="tidy-up"></a>整理

若要在完成本操作指南后进行清理并避免不必要的费用，请在 Azure 门户中删除 **IoTCentralAnalysis** 资源组。

可以从应用程序中的“管理”页删除 IoT Central 应用程序。****

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* 使用“连续数据导出”从 IoT Central 应用程序流式传输遥测数据。**
* 创建一个 Azure Databricks 环境用于分析和绘制遥测数据。

了解如何创建自定义分析后，建议接下来了解如何在 Power BI 仪表板中[可视化和分析 Azure IoT Central 数据](howto-connect-powerbi.md)。
