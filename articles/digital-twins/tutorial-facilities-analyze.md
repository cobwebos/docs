---
title: 分析 Azure 数字孪生设置中的事件 | Microsoft Docs
description: 了解如何根据本教程中的步骤使用 Azure 时序见解可视化和分析 Azure 数字孪生空间中的事件。
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323174"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>教程：使用时序见解可视化和分析 Azure 数字孪生空间中的事件

部署 Azure 数字孪生实例、预配空间并实现用于监视特定条件的自定义函数以后，即可将空间中的事件和数据可视化，查找趋势和异常。 

在[第一个教程](tutorial-facilities-setup.md)中，你配置了一栋虚构大楼的空间图，其中一个房间包含用于移动、二氧化碳和温度的传感器。 在[第二个教程](tutorial-facilities-udf.md)中，已预配了图形和用户定义的函数。 此函数监视这些传感器值并在条件适当的情况下触发通知，这里的条件适当是指房间为空房间且温度和二氧化碳水平正常。 本教程介绍如何将这些通知和来自数字孪生设置的数据与 Azure 时序见解集成。 然后，你可以将某段时间的传感器值可视化，查找各种趋势，例如，哪个房间使用频率最高、一天中最忙的时间是什么时间，等等。 也可检测异常，例如，哪些房间让人感觉又闷又热，或者大楼中某个区域发送的温度值是否始终很高，表明空调故障。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用事件中心对数据进行流式传输
> * 使用时序见解进行分析

## <a name="prerequisites"></a>先决条件

本教程假定你已[配置](tutorial-facilities-setup.md)和[预配](tutorial-facilities-udf.md) Azure 数字孪生设置。 在继续操作之前，请确保已具备以下条件：
- 一个 [Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个正在运行的数字孪生实例。
- 在工作计算机上下载并解压缩的[数字孪生 C# 示例](https://github.com/Azure-Samples/digital-twins-samples-csharp)。
- 在用于运行示例的开发计算机上安装的 [.NET Core SDK 2.1.403 或更高版本](https://www.microsoft.com/net/download)。 请运行 `dotnet --version` 以验证是否安装了正确的版本。 


## <a name="stream-data-using-event-hubs"></a>使用事件中心对数据进行流式传输
[事件中心](../event-hubs/event-hubs-about.md)服务用于创建流式传输数据所需的管道。 本部分介绍如何将事件中心创建为数字孪生和 TSI 实例之间的连接器。

### <a name="create-an-event-hub"></a>创建事件中心

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧导航面板上，单击“创建资源”。 

1. 搜索并选择“事件中心”。 单击“创建”。

1. 为事件中心命名空间输入一个**名称**，选择标准**定价层**、你的**订阅**、用于数字孪生实例的**资源组**，以及**位置**。 单击“创建”。 

1. 部署以后，请导航到事件中心命名空间部署，单击“资源”下的命名空间。

    ![事件中心命名空间](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. 在事件中心命名空间的“概览”窗格中，单击顶部的“事件中心”按钮。 
    ![事件中心](./media/tutorial-facilities-analyze/create-event-hub.png)

1. 为事件中心输入一个“名称”，然后单击“创建”。 部署以后，它会显示在事件中心命名空间的“事件中心”窗格中，其“状态”为“活动”。 单击此事件中心，打开其“概览”窗格。

1. 单击顶部的“使用者组”按钮，为使用者组输入一个名称，例如 *tsievents*。 单击“创建”。
    ![事件中心使用者组](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   创建以后，使用者组会显示在事件中心的“概览”窗格底部的列表中。 

1. 打开事件中心的“共享访问策略”窗格，然后单击“添加”按钮。 **创建**一个名为 *ManageSend* 的策略，确保所有复选框都已勾选。 

    ![事件中心连接字符串](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. 打开已创建的 ManageSend 策略，将“连接字符串--主键”和“连接字符串--辅助键”的值复制到一个临时文件中。 在下一部分，将需要这些值来创建事件中心的终结点。

### <a name="create-endpoint-for-the-event-hub"></a>为事件中心创建终结点

1. 在命令窗口中，确保当前位置为数字孪生示例的 **_occupancy-quickstart\src** 文件夹。

1. 在编辑器中打开 **_actions\createEndpoints.yaml_** 文件。 将内容替换为以下内容：

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. 将占位符 `Primary_connection_string_for_your_event_hub` 替换为事件中心的“连接字符串--主键”的值。 确保此连接字符串的格式如下：
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. 将占位符 `Secondary_connection_string_for_your_event_hub` 替换为事件中心的“连接字符串--辅助键”的值。 确保此连接字符串的格式如下： 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. 将占位符 `Name_of_your_Event_Hubs_namespace` 替换为事件中心命名空间的名称。

    > [!IMPORTANT]
    > 输入所有值，不带任何引号。 在 *YAML* 文件中，请确保在冒号之后至少有一个空格字符。 也可使用任何联机 YAML 验证程序（例如[此工具](https://onlineyamltools.com/validate-yaml)）来验证 *YAML* 文件内容。


1. 保存并关闭该文件。 在命令窗口中运行以下命令，在系统提示时使用 Azure 帐户登录。

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   它为事件中心创建两个终结点。

   ![事件中心的终结点](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>使用时序见解进行分析

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“创建资源”。 

1. 搜索并选择新的“时序见解”资源。 单击“创建”。

1. 为时序见解实例输入一个**名称**，然后选择你的**订阅**。 选择用于数字孪生实例的“资源组”，然后选择“位置”。 单击“创建”。

    ![创建 TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. 部署以后，打开时序见解环境，然后打开其“事件源”窗格。 单击顶部的“添加”按钮，添加使用者组。

1. 在“新建事件源”窗格中输入一个**名称**，确保其他值选择正确。 选择 *ManageSend* 作为“事件中心策略名称”，，然后选择在上一部分创建的使用者组，作为“事件中心使用者组”。 单击“创建”。

    ![TSI 事件源](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. 打开时序见解环境的“概览”窗格，单击顶部的“转到环境”按钮。 如果出现事件访问警告，则请打开 TSI 实例的“数据访问策略”窗格，单击“添加”，选择“参与者”作为角色，然后选择适当的用户。

1. “转到环境”按钮会打开[时序见解资源管理器](../time-series-insights/time-series-insights-explorer.md)。 如果它不显示任何事件，请模拟设备事件，方法是：导航到数字孪生示例的 **_device-connectivity_** 项目，然后运行 `dotnet run`。

1. 生成一些模拟的事件以后，请返回到时序见解资源管理器，单击顶部的刷新按钮。 此时会看到系统针对模拟的传感器数据生成分析表格。 

    ![TSI 资源管理器](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. 然后，可以在时序见解资源管理器中针对来自房间、传感器和其他资源的不同事件和数据生成图表和热度地图。 在左侧单击名为“度量值”和“拆分方式”的下拉框，创建自己的可视化效果。 例如，选择“事件”作为“度量值”，选择“DigitalTwins-SensorHardwareId”作为“拆分方式”，以便为每个传感器生成热度地图，类似于下图：

    ![TSI 资源管理器](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>清理资源

如果不希望继续探索 Azure 数字孪生，可以删除本教程中创建的资源：

1. 在 [Azure 门户](http://portal.azure.com)的左侧菜单中单击“所有资源”，选择数字孪生资源组，然后单击“删除”将其删除。
2. 也可根据需要继续删除工作计算机上的示例应用程序。 


## <a name="next-steps"></a>后续步骤

请继续阅读下一篇文章，详细了解 Azure 数字孪生中的空间智能图和对象模型。 
> [!div class="nextstepaction"]
> [了解数字孪生对象模型和空间智能图](concepts-objectmodel-spatialgraph.md)

