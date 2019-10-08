---
title: 将数据从 IoT 中心引入 Azure 数据资源管理器
description: 本文介绍如何从 IoT 中心将数据引入（加载）到 Azure 数据资源管理器。
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: ef66e6a69b6d33b16a558293fe29b8adf51cd137
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996752"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>将数据从 IoT 中心引入 Azure 数据资源管理器（预览版）

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器提供来自 IoT 中心的引入（数据加载），这是一个大型数据流式处理平台和 IoT 引入服务。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 使用数据库名称*testdb*创建[一个测试群集和数据库](create-cluster-database-portal.md)。

* 用于模拟设备的[示例应用程序](https://github.com/Azure-Samples/azure-iot-samples-csharp)和文档。

* 用于运行示例应用的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="create-an-iot-hub"></a>创建 Iot 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>向 IoT 中心注册设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 数据资源管理器中创建目标表

现在，你在 Azure 数据资源管理器中创建了 IoT 中心将向其发送数据的表。 在群集和配置了的数据库中创建[**表。** ](#prerequisites)

1. 在 Azure 门户中导航到群集，然后选择“查询”。

    ![门户中的 ADX 查询](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 将以下命令复制到窗口中，然后选择“运行”以创建将接收引入数据的表 (TestTable)。

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![运行创建查询](media/ingest-data-iot-hub/run-create-query.png)

1. 将以下命令复制到窗口中，然后选择“运行”将传入的 JSON 数据映射到表 (TestTable) 的列名和数据类型。

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>将 Azure 数据资源管理器表连接到 IoT 中心

现在，可以从 Azure 数据资源管理器连接到 IoT 中心。 此连接完成后，流入到 iot 中心的数据将流式传输到[您创建的目标表](#create-a-target-table-in-azure-data-explorer)。

1. 在工具栏上选择 "**通知**"，验证 IoT 中心部署是否成功。

1. 在创建的群集下，选择 "**数据库**"，然后选择**testdb**创建的数据库。
    
    ![选择测试数据库](media/ingest-data-iot-hub/select-database.png)

1. 选择“数据引入”，然后选择“添加数据连接”。 然后使用以下信息填写窗体。 完成后，选择 "**创建**"。

    ![IoT 中心连接](media/ingest-data-iot-hub/iot-hub-connection.png)

    **数据源**：

    **设置** | **字段说明**
    |---|---|
    | 数据连接名称 | 要在 Azure 中创建的连接的名称数据资源管理器
    | IoT 中心 | IoT 中心名称 |
    | 共享访问策略 | 共享访问策略的名称。 必须具有读取权限 |
    | 使用者组 |  IoT 中心内置终结点中定义的使用者组 |
    | 事件系统属性 | [IoT 中心事件系统属性](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages)。 添加系统属性时，[创建](/azure/kusto/management/tables#create-table)或[更新](/azure/kusto/management/tables#alter-table-and-alter-merge-table)表架构和[映射](/azure/kusto/management/mappings)以包括所选属性。 | | | 

    > [!NOTE]
    > 如果是[手动故障转移](/azure/iot-hub/iot-hub-ha-dr#manual-failover)，则必须重新创建数据连接。

    **目标表**：

    路由引入数据有两个选项：静态和动态。 
    本文将使用静态路由，需在其中指定表名、数据格式和映射。 因此，请让“我的数据包含路由信息”保留未选中状态。

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 表 | TestTable | 在**testdb**中创建的表。 |
    | 数据格式 | *JSON* | 支持的格式为 Avro、CSV、JSON、多行 JSON、PSV、SOHSV、SCSV、TSV、TSVE 和 TXT。 |
    | 列映射 | TestMapping | 在**testdb**中创建的[映射](/azure/kusto/management/mappings)，用于将传入 JSON 数据映射到**testdb**的列名称和数据类型。 对于 JSON、多行 JSON 和 AVRO 是必需的，对于其他格式则是可选的。|
    | | |

    > [!NOTE]
    > * 选择“我的数据包含路由信息”以使用动态路由，其中你的数据包含必要的路由信息，如[示例应用](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)注释中所示。 如果同时设置了静态和动态属性，则动态属性将覆盖静态属性。 
    > * 只有在创建数据连接后排队的事件引入。

## <a name="generate-sample-data-for-testing"></a>生成用于测试的示例数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 从 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip 下载示例 C# 项目并提取 ZIP 存档。

1. 在本地终端窗口中，导航到示例 C# 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

1. 在所选文本编辑器中打开 SimulatedDevice.cs 文件。

    将 `s_connectionString` 变量的值替换为设备连接字符串，将[设备注册到 IoT 中心](#register-a-device-to-the-iot-hub)。 然后将更改保存到 SimulatedDevice.cs 文件。

1. 在本地终端窗口中，运行以下命令以安装模拟设备应用程序所需的包：

    ```cmd/sh
    dotnet restore
    ```

1. 在本地终端窗口中，运行以下命令，生成并运行模拟设备应用程序：

    ```cmd/sh
    dotnet run
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>查看数据流

在应用生成数据的情况下，你现在可以看到从 IoT 中心流向群集中表的数据流。

1. 在 Azure 门户的 IoT 中心下，你会在应用运行时看到活动中的峰值。

    ![IoT 中心指标](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. 若要检查到目前为止已向数据库发送的消息数，请在测试数据库中运行以下查询。

    ```Kusto
    TestTable
    | count
    ```

1. 若要查看消息的内容，请运行以下查询：

    ```Kusto
    TestTable
    ```

    结果集：
    
    ![显示引入数据结果](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure 数据资源管理器具有用于数据引入的聚合（批处理）策略，旨在优化引入过程。 默认情况下，该策略配置为 5 分钟或 500 MB 数据，因此你可能会遇到延迟。 有关聚合选项，请参阅[批处理策略](/azure/kusto/concepts/batchingpolicy)。 
    > * 配置表以支持流式处理并消除响应时间延迟。 请参阅[流式处理策略](/azure/kusto/concepts/streamingingestionpolicy)。 

## <a name="clean-up-resources"></a>清理资源

如果不打算再次使用 IoT 中心，请清理**测试中心-rg**，以避免产生费用。

1. 在 Azure 门户的最左侧选择“资源组”，，然后选择创建的资源组。  

    如果左侧菜单处于折叠状态，请选择 ![“展开”按钮](media/ingest-data-event-hub/expand.png) 将其展开。

   ![选择要删除的资源组](media/ingest-data-event-hub/delete-resources-select.png)

1. 在“test-resource-group”下，选择“删除资源组”。

1. 在新窗口中，键入要删除的资源组的名称 (test-hub-rg)，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据资源管理器中查询数据](web-query-data.md)
