---
title: 通过自定义分析扩展 Azure IoT Central |Microsoft Docs
description: 作为解决方案开发人员，配置一个 IoT Central 应用程序以执行自定义分析和可视化。 此解决方案使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a29cae2fabe1542a7498bca19dc0a6e147d1d024
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895145"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks-preview-features"></a>使用 Azure Databricks （预览功能）通过自定义分析扩展 Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本操作方法指南向解决方案开发人员展示了如何使用自定义分析和可视化功能扩展 IoT Central 应用程序。 该示例使用[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)工作区来分析 IoT Central 遥测流，并生成可视化对象（如[框](https://wikipedia.org/wiki/Box_plot)图）。

本操作方法指南介绍了如何将 IoT Central 扩展到其对[内置分析工具](./howto-create-custom-analytics.md)的功能。

本操作方法指南介绍如何执行以下操作：

* 使用*连续数据导出*从 IoT Central 应用程序流式传输遥测数据。
* 创建用于分析和绘制设备遥测的 Azure Databricks 环境。

## <a name="prerequisites"></a>必备组件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

使用以下设置在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建 IoT Central 应用程序：

| 设置 | 值 |
| ------- | ----- |
| 付款计划 | 即用即付 |
| 应用程序模板 | 应用商店内分析-条件监视 |
| 应用程序名称 | 接受默认值或选择自己的名称 |
| URL | 接受默认值或选择自己的唯一 URL 前缀 |
| Directory | 你的 Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 区域 | 最近的区域 |

本文中的示例和屏幕截图使用**美国**区域。 选择靠近你的位置，并确保在同一区域中创建所有资源。

此应用程序模板包含两个用于发送遥测的模拟恒温器设备。

### <a name="resource-group"></a>资源组

使用 Azure 门户创建名为**IoTCentralAnalysis**的[资源组](https://portal.azure.com/#create/Microsoft.ResourceGroup)，以包含您创建的其他资源。 在 IoT Central 应用程序所在的同一位置创建 Azure 资源。

### <a name="event-hubs-namespace"></a>事件中心命名空间

使用 Azure 门户创建具有以下设置的[事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub)：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| 位置 | 美国东部 |
| 吞吐量单位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作区

使用 Azure 门户创建具有以下设置的[Azure Databricks 服务](https://portal.azure.com/#create/Microsoft.Databricks)：

| 设置 | 值 |
| ------- | ----- |
| 工作区名称    | 选择工作区名称 |
| 订阅 | 订阅 |
| 资源组 | IoTCentralAnalysis |
| 位置 | 美国东部 |
| 定价层 | 标准 |

创建所需的资源后， **IoTCentralAnalysis**资源组将如以下屏幕截图所示：

![IoT Central 分析资源组](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可以将 IoT Central 应用程序配置为将遥测数据持续导出到事件中心。 在本部分中，将创建一个事件中心来接收来自 IoT Central 应用程序的遥测数据。 事件中心将遥测传递到流分析作业进行处理。

1. 在 Azure 门户中，导航到事件中心命名空间，然后选择 " **+ 事件中心**"。
1. 将事件中心命名为**centralexport**，然后选择 "**创建**"。
1. 在命名空间中的事件中心列表中，选择 " **centralexport**"。 然后选择 "**共享访问策略**"。
1. 选择“+ 添加”。 创建名为 "**侦听**" 的策略。
1. 当策略准备就绪后，请在列表中选择它，然后复制 "**连接字符串-主键**" 值。
1. 记下此连接字符串，稍后在将 Databricks 笔记本配置为从事件中心读取时使用该字符串。

事件中心命名空间如以下屏幕截图所示：

![事件中心命名空间](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中配置导出

在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上，导航到从 Contoso 模板创建的 IoT Central 应用程序。 在本部分中，将配置应用程序，以便将遥测从其模拟设备流式传输到事件中心。 若要配置导出：

1. 导航到**数据导出**页，依次选择 " **+ 新建**" 和 " **Azure 事件中心**"。
1. 使用以下设置配置导出，然后选择 "**保存**"：

    | 设置 | 值 |
    | ------- | ----- |
    | 显示名称 | 导出到事件中心 |
    | Enabled | 启用 |
    | 事件中心命名空间 | 事件中心命名空间名称 |
    | 事件中心 | centralexport |
    | 度量 | 启用 |
    | 设备 | 关闭 |
    | 设备模板 | 关闭 |

![数据导出配置](media/howto-create-custom-analytics/cde-configuration.png)

继续之前，请等待导出状态为 "**正在运行**"。

## <a name="configure-databricks-workspace"></a>配置 Databricks 工作区

在 Azure 门户中，导航到 Azure Databricks 服务，然后选择 "**启动工作区**"。 此时会在浏览器中打开新选项卡，并登录到工作区。

### <a name="create-a-cluster"></a>创建群集

在 " **Azure Databricks** " 页上的 "常见任务" 列表中，选择 "**新建群集**"。

使用下表中的信息创建群集：

| 设置 | 值 |
| ------- | ----- |
| 群集名称 | centralanalysis |
| 群集模式 | 标准 |
| Databricks Runtime 版本 | 5.5 LTS （Scala 2.11，Spark 2.4.3） |
| Python 版本 | 3 |
| 启用自动缩放 | 否 |
| 在处于非活动状态分钟后终止 | 30 |
| 辅助角色类型 | Standard_DS3_v2 |
| 工作节点 | 1 |
| 驱动程序类型 | 与辅助角色相同 |

创建群集可能需要几分钟时间，请等待群集创建完成，然后再继续。

### <a name="install-libraries"></a>安装库

在 "**群集**" 页上，等待群集状态为 "**正在运行**"。

以下步骤说明如何将示例所需的库导入群集：

1. 在 "**群集**" 页上，等待**centralanalysis**交互群集的状态为 "**正在运行**"。

1. 选择该群集，然后选择 "**库**" 选项卡。

1. 在 "**库**" 选项卡上，选择 "**安装新**的"。

1. 在 "**安装库**" 页上，选择 " **Maven** " 作为库源。

1. 在 "**坐标**" 文本框中，输入以下值： `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 选择 "**安装**" 以在群集上安装库。

1. 此时会**安装**库状态：

    ![已安装库](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>导入 Databricks 笔记本

使用以下步骤来导入包含 Python 代码的 Databricks 笔记本，以分析和可视化 IoT Central 遥测：

1. 导航到 Databricks 环境中的 "**工作区**" 页面。 选择帐户名旁边的下拉列表，然后选择 "**导入**"。

1. 选择 "从 URL 导入"，并输入以下地址： " [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要导入笔记本，请选择 "**导入**"。

1. 选择**工作区**以查看导入的笔记本：

    ![导入的笔记本](media/howto-create-custom-analytics/import-notebook.png)

1. 编辑第一个 Python 单元中的代码，添加之前保存的事件中心连接字符串：

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>运行分析

若要运行分析，必须将笔记本连接到群集：

1. 选择 "**分离**"，然后选择 " **centralanalysis** " 群集。
1. 如果群集未运行，则启动它。
1. 若要启动笔记本，请选择 "运行" 按钮。

最后一个单元格中可能会出现错误。 如果是这样，请检查上一个单元格是否正在运行，等待几分钟，让某些数据写入存储，然后再次运行最后一个单元格。

### <a name="view-smoothed-data"></a>查看平滑数据

在笔记本中，向下滚动到单元格14，查看移动平均湿度按设备类型的绘图。 当流式传输遥测到达时，此绘图会不断更新：

![平滑遥测绘图](media/howto-create-custom-analytics/telemetry-plot.png)

可以在笔记本中调整图表的大小。

### <a name="view-box-plots"></a>视图框图形

在笔记本中，向下滚动到 "单元" 20 以查看[框的绘图](https://en.wikipedia.org/wiki/Box_plot)。 盒须图基于静态数据，因此，若要对其进行更新，则必须重新运行单元：

![盒图形](media/howto-create-custom-analytics/box-plots.png)

可以调整笔记本中图形的大小。

## <a name="tidy-up"></a>整理

若要在此操作说明和避免不必要的成本后进行整理，请删除 Azure 门户中的**IoTCentralAnalysis**资源组。

可以从应用程序内的 "**管理**" 页中删除 IoT Central 应用程序。

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* 使用*连续数据导出*从 IoT Central 应用程序流式传输遥测数据。
* 创建用于分析和绘制遥测数据的 Azure Databricks 环境。

了解如何创建自定义分析后，建议下一步是了解如何[在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据](../core/howto-connect-powerbi.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)。
