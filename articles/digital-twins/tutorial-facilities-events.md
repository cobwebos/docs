---
title: 教程：从 IoT 空间捕获设备事件 - Azure 数字孪生 | Microsoft Docs
description: 了解如何使用本教程中的步骤将 Azure 数字孪生与逻辑应用集成，以便从空间接收通知。
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 7700c61a978532a63fc5b3298d45b8e7041dba40
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790340"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>教程：使用逻辑应用从 Azure 数字孪生空间接收通知

部署 Azure 数字孪生实例、预配空间并实现用于监视特定条件的自定义函数以后，即可在受监视的条件满足时通过电子邮件通知办公室主任。

在[第一个教程](tutorial-facilities-setup.md)中，你配置了一栋虚构大楼的空间图， 其中一个房间包含用于移动、二氧化碳和温度的传感器。 在[第二个教程](tutorial-facilities-udf.md)中，你预配了图形和一个用户定义函数，用于监视这些传感器值并在房间为空且温度和二氧化碳处于某个令人舒适的范围内时触发通知。 

本教程介绍如何将这些通知与 Azure 逻辑应用集成，以便在此类房间可用时发送电子邮件。 办公室主任可以根据此信息帮助员工预订工作效率最高的会议室。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将事件与 Azure 事件网格集成。
> * 使用逻辑应用发出事件通知。

## <a name="prerequisites"></a>先决条件

本教程假定你已[配置](tutorial-facilities-setup.md)和[预配](tutorial-facilities-udf.md) Azure 数字孪生设置。 在继续操作之前，请确保已具备以下条件：

- 一个 [Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个正在运行的数字孪生实例。
- 在工作计算机上下载并解压缩的[数字孪生 C# 示例](https://github.com/Azure-Samples/digital-twins-samples-csharp)。
- 在用于运行示例的开发计算机上安装的 [.NET Core SDK 2.1.403 或更高版本](https://www.microsoft.com/net/download)。 请运行 `dotnet --version` 以验证是否安装了正确的版本。
- 用于发送通知电子邮件的 [Office 365](https://products.office.com/home) 帐户。

> [!TIP]
> 如果要预配新实例，请使用唯一的数字孪生实例名称。

## <a name="integrate-events-with-event-grid"></a>将事件与事件网格集成

在此部分，请设置一个[事件网格](../event-grid/overview.md)，以便从 Azure 数字孪生实例收集事件，然后将事件重定向到[事件处理程序](../event-grid/event-handlers.md)，例如逻辑应用。

### <a name="create-an-event-grid-topic"></a>创建事件网格主题

[事件网格主题](../event-grid/concepts.md#topics)提供一个界面，用于路由用户定义的函数生成的事件。 

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择“创建资源”  。 

1. 搜索并选择“事件网格主题”  。 选择“创建”  。

1. 为事件网格主题输入一个“名称”，然后选择“订阅”。   选择用于数字孪生实例的或者为其创建的“资源组”  ，然后选择“位置”。  选择“创建”  。 

    [![创建事件网格主题](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. 从资源组浏览到事件网格主题，选择“概览”，将“主题终结点”的值复制到某个临时文件。   下一部分将需要此 URL。 

1. 选择“访问密钥”，  将**密钥 1** 和**密钥 2** 复制到某个临时文件。 在下一部分，将需要这些值来创建终结点。

    [![事件网格密钥](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>为事件网格主题创建终结点

1. 在命令窗口中，确保当前位置为数字孪生示例的 **occupancy-quickstart\src** 文件夹。

1. 在 Visual Studio Code 编辑器中打开  actions\createEndpoints.yaml 文件。 确保其包含以下内容：

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. 将占位符 `<Primary connection string for your Event Grid>` 替换为密钥 1 的值  。

1. 将占位符 `<Secondary connection string for your Event Grid>` 替换为密钥 2 的值  。

1. 将**路径**的占位符替换为事件网格主题的路径。 从**主题终结点** URL 中删除 **https://** 以及尾随的资源路径即可获取此路径。 它看起来应该类似于此格式：*yourEventGridName.yourLocation.eventgrid.azure.net*。

    > [!IMPORTANT]
    > 输入所有值，不带任何引号。 在 YAML 文件中，请确保在冒号之后至少有一个空格字符。 也可使用任何联机 YAML 验证程序（例如[此工具](https://onlineyamltools.com/validate-yaml)）来验证 YAML 文件内容。

1. 保存并关闭该文件。 在命令窗口中，当出现提示时，请运行以下命令并登录。 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   此命令为事件网格创建终结点。 

   [![事件网格的终结点](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>使用逻辑应用发出事件通知

可以通过 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)服务为从其他服务接收的事件创建自动化任务。 在此部分，请设置逻辑应用，以便借助[事件网格主题](../event-grid/overview.md)为从空间传感器路由的事件创建电子邮件通知。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“创建资源”  。

1. 搜索并选择新的“逻辑应用”资源  。 选择“创建”  。

1. 为逻辑应用资源输入一个**名称**，然后选择你的**订阅**、**资源组**和**位置**。 选择“创建”  。

    [![创建逻辑应用资源](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. 在逻辑应用资源部署后将其打开，然后打开“逻辑应用设计器”窗格。  

1. 选择“当事件网格资源事件发生时”触发器。  展开“Azure 事件网格”选项，并在系统提示时使用 Azure 帐户登录到租户。  如果系统提示，针对事件网格资源选择“允许访问”  。 选择“继续”。 

1. 在“当资源事件发生时”窗口中，执行以下操作：  
   
   a. 选择曾经用于创建事件网格主题的**订阅**。

   b. 选择 **Microsoft.EventGrid.Topics** 作为**资源类型**。

   c. 从**资源名称**对应的下拉框中选择事件网格资源。

   [![“逻辑应用设计器”窗格](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. 选择“新步骤”  按钮。

1. 在“选择操作”窗口中，执行以下操作： 

   a. 搜索“分析 json”短语，然后选择“分析 JSON”操作。  

   b. 在“内容”字段的“动态内容”列表中选择“正文”。   

   c. 选择“使用示例有效负载生成架构”。  粘贴以下 JSON 有效负载，然后选择“完成”。 

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    此有效负载包含虚构值。 逻辑应用使用此示例有效负载生成一个*架构*。

    [![适用于事件网格的逻辑应用“分析 JSON”窗口](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. 选择“新步骤”  按钮。

1. 在“选择操作”窗口中，执行以下操作： 

   a. 选择“控制”>“条件”  或从“操作”  列表中搜索“条件”  。 

   b. 在第一个“选择值”文本框的“分析 JSON”窗口的“动态内容”列表中选择“eventType”。    

   c. 在第二个“选择值”文本框中输入“`UdfCustom`”。 

   [![所选条件](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. 在 **If true** 窗口中，执行以下操作：

   a. 选择“添加操作”，然后选择“Office 365 Outlook”。  

   b. 从“操作”列表中选择“发送电子邮件 (V2)”。   选择“登录”，  使用电子邮件帐户凭据。 如果系统提示，请选择“允许访问”。 

   c. 在“收件人”框中，输入用于接收通知的电子邮件 ID。  在“主题”中  输入文本“有关空间中空气质量差的数字孪生通知”，  然后从**分析 JSON** 的“动态内容”列表中选择“TopologyObjectId”。  

   d. 在同一窗口中的“正文”  下，输入与此类似的文本：**检测到房间内空气质量差，需要调整温度**。 请根据需要使用“动态内容”列表中的元素进行详细阐述。 

   [![逻辑应用的“发送电子邮件”选项](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. 选择“逻辑应用设计器”窗格顶部的“保存”按钮   。

1. 确保模拟传感器数据，方法是：在命令窗口中浏览到数字孪生示例的 **device-connectivity** 文件夹，然后运行 `dotnet run`。

数分钟后，就会开始从此逻辑应用资源中获取电子邮件通知。 

   [![电子邮件通知](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

若要停止接收这些电子邮件，请转到门户中的逻辑应用资源，然后选择“概览”窗格。  选择“禁用”。 

## <a name="clean-up-resources"></a>清理资源

如果不希望继续探索 Azure 数字孪生，可以删除本教程中创建的资源：

1. 在 [Azure 门户](https://portal.azure.com)的左菜单中依次选择“所有资源”、  数字孪生资源组、“删除”。 

    > [!TIP]
    > 如果在删除数字孪生实例时遇到麻烦，请使用已推出的包含修补程序的服务更新。 请重新尝试删除实例。

2. 可以根据需要删除工作计算机上的示例应用程序。

## <a name="next-steps"></a>后续步骤

若要了解如何可视化传感器数据、分析趋势和查找异常，请继续阅读下一教程：

> [!div class="nextstepaction"]
> [教程：使用时序见解直观显示和分析 Azure 数字孪生空间中的事件](tutorial-facilities-analyze.md)

也可详细了解 Azure 数字孪生中的空间智能图和对象模型：

> [!div class="nextstepaction"]
> [了解数字孪生对象模型和空间智能图](concepts-objectmodel-spatialgraph.md)
