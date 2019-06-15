---
title: 使用自定义分析来扩展 Azure IoT Central |Microsoft Docs
description: 作为解决方案开发人员，IoT Central 应用程序配置为执行自定义分析和可视化效果。 此解决方案使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743435"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>使用自定义分析来扩展 Azure IoT Central

本操作方法指南介绍了，作为解决方案开发人员如何扩展 IoT Central 应用程序与自定义分析和可视化效果。 该示例使用[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)工作区来分析 IoT Central 遥测数据流，并生成可视化效果，如[框绘图](https://wikipedia.org/wiki/Box_plot)。

本操作方法指南演示如何以超出其已功能与 IoT Central[内置分析工具](howto-create-analytics.md)。

在本操作方法指南中，你了解如何：

* Stream 来自 IoT Central 应用程序中使用的遥测*持续的数据导出*。
* 创建 Azure Databricks 环境用于分析和绘制的设备遥测数据。

## <a name="prerequisites"></a>必备组件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

创建 IoT Central 应用程序从[Azure IoT Central-我的应用程序](https://aka.ms/iotcentral)页具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 付款计划 | 即用即付 |
| 应用程序模板 | 示例 Contoso |
| 应用程序名称 | 接受默认值或选择自己的名称 |
| URL | 接受默认值或选择自己唯一的 URL 前缀 |
| Directory | Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 区域 | 美国东部 |

示例和屏幕截图，在此文章使用**美国东部**区域。 选择离你近的位置，并确保在同一区域中创建的所有资源。

### <a name="resource-group"></a>资源组

使用[Azure 门户创建资源组](https://portal.azure.com/#create/Microsoft.ResourceGroup)称为**IoTCentralAnalysis**包含您创建的其他资源。 在与 IoT 中心应用程序相同的位置中创建 Azure 资源。

### <a name="event-hubs-namespace"></a>事件中心命名空间

使用[Azure 门户创建事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择你的命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| Location | 美国东部 |
| 吞吐量单位 | 第 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作区

使用[Azure 门户创建 Azure Databricks 服务](https://portal.azure.com/#create/Microsoft.Databricks)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 工作区名称    | 选择工作区名称 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| Location | 美国东部 |
| 定价层 | 标准 |

创建后所需的资源，你**IoTCentralAnalysis**如以下屏幕截图所示的资源组：

![IoT Central 分析资源组](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可以配置 IoT Central 应用程序可以持续将遥测数据导出到事件中心。 在本部分中，您将创建事件中心以接收来自 IoT 中心应用程序的遥测数据。 事件中心将遥测数据传递给 Stream Analytics 作业进行处理。

1. 在 Azure 门户中，导航到事件中心命名空间，并选择 **+ 事件中心**。
1. 事件中心命名**centralexport**，然后选择**创建**。
1. 在你的命名空间中的事件中心的列表中选择**centralexport**。 然后选择**共享访问策略**。
1. 选择 **+ 添加**。 创建一个名为策略**侦听**与**侦听**声明。
1. 策略准备就绪后，在列表中，选择它，然后将复制**连接字符串-主键**值。
1. 请记下的此连接字符串，则当你将从事件中心读取在 Databricks 笔记本配置为使用更高版本。

事件中心命名空间如以下屏幕截图所示：

![事件中心命名空间](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心中配置导出

导航到[IoT 中心应用程序](https://aka.ms/iotcentral)从 Contoso 模板创建。 在本部分中，将配置要流式传输到事件中心的遥测数据从其模拟设备的应用程序。 若要配置导出：

1. 导航到**连续数据导出**页上，选择 **+ 新建**，然后**Azure 事件中心**。
1. 使用以下设置配置导出，然后选择**保存**:

    | 设置 | 值 |
    | ------- | ----- |
    | 显示名称 | 导出到事件中心 |
    | Enabled | 启用 |
    | 事件中心命名空间 | 事件中心命名空间名称 |
    | 事件中心 | centralexport |
    | 度量 | 启用 |
    | 设备 | 关闭 |
    | 设备模板 | 关闭 |

![连续数据的导出配置](media/howto-create-custom-analytics/cde-configuration.png)

导出状态是等到**运行**，并继续。

## <a name="configure-databricks-workspace"></a>配置 Databricks 工作区

在 Azure 门户中，导航到 Azure Databricks 服务，然后选择**启动工作区**。 新选项卡将在你的浏览器中打开并登录到你的工作区。

### <a name="create-a-cluster"></a>创建群集

上**Azure Databricks**页上，选择的常见任务列表下**新群集**。

使用下表中的信息来创建群集：

| 设置 | 值 |
| ------- | ----- |
| 群集名称 | centralanalysis |
| 群集模式 | 标准 |
| Databricks 运行时版本 | 5.3 (Scala 2.11, Spark 2.4.0) |
| Python 版本 | 3 |
| 启用自动缩放 | 否 |
| 非活动分钟数后终止 | 30 |
| 辅助角色类型 | Standard_DS3_v2 |
| 工作节点 | 第 |
| 驱动程序类型 | 辅助角色相同 |

创建群集可能需要几分钟时间，等待群集创建完成才能继续。

### <a name="install-libraries"></a>安装库

上**群集**页上，等待，直到群集状态为**运行**。

以下步骤演示了如何将您的示例需要库导入到该群集：

1. 上**群集**页上，等待的状态**centralanalysis**交互式群集是**运行**。

1. 选择群集，然后选择**库**选项卡。

1. 上**库**选项卡上，选择**安装新**。

1. 上**安装库**页上，选择**Maven**作为库源。

1. 在中**协调**文本框中，输入以下值： `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 选择**安装**以在群集上安装库。

1. 库状态现已**已安装**:

    ![安装库](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>导入 Databricks notebook

使用以下步骤导入包含用于分析和可视化 IoT 中心遥测数据的 Python 代码的 Databricks notebook:

1. 导航到**工作区**Databricks 环境中的页。 选择你的帐户名称旁边的下拉列表，然后选择**导入**。

1. 选择从 URL 导入并输入以下地址： [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要导入笔记本，请选择**导入**。

1. 选择**工作区**若要查看已导入的笔记本：

    ![导入的笔记本](media/howto-create-custom-analytics/import-notebook.png)

1. 编辑以前保存的事件中心连接字符串添加的第一个 Python 单元中的代码：

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

1. 选择**Detached** ，然后选择**centralanalysis**群集。
1. 如果群集未运行，启动它。
1. 若要启动笔记本，请选择运行按钮。

可能会看到的最后一个单元中的错误。 如果是这样，检查上一单元格正在运行、 等待某些数据写入到存储，一分钟，然后重新运行最后一个单元格。

### <a name="view-smoothed-data"></a>查看经过平滑处理数据

在笔记本中向下滚动单元格 14，若要查看的设备类型的滚动平均湿度。 此绘图不断更新流式处理遥测数据到达时：

![经过平滑处理遥测绘图](media/howto-create-custom-analytics/telemetry-plot.png)

您可以调整大小笔记本中的图表。

### <a name="view-box-plots"></a>绘制视图箱线图

在笔记本中向下滚动到单元格 20，若要查看[框绘图](https://en.wikipedia.org/wiki/Box_plot)。 框绘图均基于静态数据，因此将其更新必须重新运行该单元格：

![绘制箱线图](media/howto-create-custom-analytics/box-plots.png)

您可以调整大小在 notebook 中的绘图。

## <a name="tidy-up"></a>整理

若要整理后本操作指南并避免不必要的费用，删除**IoTCentralAnalysis**在 Azure 门户中的资源组。

可以删除从 IoT 中心应用程序**管理**应用程序中的页。

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* Stream 来自 IoT Central 应用程序中使用的遥测*持续的数据导出*。
* 创建 Azure Databricks 环境用于分析和绘制遥测数据。

您已经知道了如何创建自定义分析，建议下一步是了解如何[可视化和分析你在 Power BI 仪表板中的 Azure IoT Central 数据](howto-connect-powerbi.md)。
