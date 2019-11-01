---
title: 教程：创建 Azure 时序见解环境 | Microsoft Docs
description: 本教程介绍如何创建时序见解环境，并在其中填充模拟设备的数据。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 9ca60b876272df15d306ac7fba2dc61875db6d06
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989657"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>教程：创建 Azure 时序见解环境

本教程逐步讲解如何创建 Azure 时序见解环境，并在其中填充模拟设备的数据。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建时序见解环境。
> * 创建包含 IoT 中心的设备模拟解决方案。
> * 将时序见解环境连接到 IoT 中心。
> * 运行设备模拟，将数据流式传输到时序见解环境。
> * 验证模拟的遥测数据。

> [!IMPORTANT]
> 注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。

## <a name="prerequisites"></a>先决条件

* 所用的 Azure 登录帐户还必须是订阅的“所有者”角色的成员。  有关详细信息，请参阅[使用基于角色的访问控制和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="review-video"></a>查看视频

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>了解如何使用 Azure IoT 解决方案加速器生成数据和开始使用时序见解。 </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>概述

时序见解环境是收集和存储设备数据的地方。 在数据存储后，[Azure 时序见解资源管理器](time-series-quickstart.md)和[时序见解查询 API](/rest/api/time-series-insights/ga-query-api) 可用来查询和分析数据。

Azure IoT 中心是本教程中的所有设备（模拟设备或物理设备）用来安全地连接到 Azure 云并向其传输数据的事件源。

本教程还使用 [IoT 解决方案加速器](https://www.azureiotsolutions.com)来生成示例遥测数据并将其流式传输到 IoT 中心。

>[!TIP]
> [IoT 解决方案加速器](https://www.azureiotsolutions.com)提供企业级的预配置解决方案，可用于加速自定义 IoT 解决方案的开发。

## <a name="create-a-device-simulation"></a>创建设备模拟

首先，创建设备模拟解决方案，该解决方案会生成测试数据，用于填充时序见解环境。

1. 在单独的窗口或标签页中访问 [azureiotsolutions.com](https://www.azureiotsolutions.com)。 使用相同的 Azure 订阅帐户登录，然后选择“设备模拟”加速器。  选择“立即试用”。 

   [![运行设备模拟加速器](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. 在“创建设备模拟”  解决方案页上输入所需的参数。

   参数|说明
   ---|---
   **部署名称** | 此唯一值用于创建新资源组。 将会创建列出的 Azure 资源并将其分配到资源组。
   **Azure 订阅** | 指定在上一部分用于创建时序见解环境的同一订阅。
   **部署选项** | 选择“预配新的 IoT 中心”  可创建特定于本教程的新 IoT 中心。
   **Azure 位置** | 指定在上一部分用于创建时序见解环境的同一区域。

   完成后选择“创建”，以预配解决方案的 Azure 资源。  完成此过程可能最多需要 20 分钟的时间。

   [![预配设备模拟解决方案](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. 完成预配后，新解决方案上方的文本会从“正在预配”更改为“就绪”。  

   >[!IMPORTANT]
   > 此时不要选择“启动”！  请让此网页保持打开状态，因为稍后需要返回此处。

   [![设备模拟解决方案预配完成](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. 现在，在 Azure 门户中检查新建的资源。 在“资源组”页上，可以看到使用最后一个步骤中提供的“解决方案名称”新建了一个资源组。   请记下已为设备模拟创建的资源。

   [![设备模拟资源](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>创建环境

其次，在 Azure 订阅中创建时序见解环境。

1. 使用 Azure 订阅帐户登录到 [Azure 门户](https://portal.azure.com)。 
1. 选择左上角的“+ 创建资源”。  
1. 依次选择“物联网”类别、“时序见解”   。 

   [![选择时序见解环境资源](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. 在“时序见解环境”页上，填写必需的参数。 

   参数|说明
   ---|---
   **环境名称** | 为时序见解环境选择唯一名称。 该名称由时序见解资源管理器和[查询 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) 使用。
   **订阅** | 订阅是 Azure 资源的容器。 选择一个订阅，以便创建时序见解环境。
   **资源组** | 资源组是 Azure 资源的容器。 为时序见解环境资源选择现有的资源组或创建新的资源组。
   **位置** | 为时序见解环境选择数据中心区域。 为了避免额外的延迟，请在与其他 IoT 资源相同的区域中创建时序见解环境。
   **层** | 选择所需的吞吐量。 选择“S1”  。
   **容量** | 容量是应用于与所选 SKU 关联的入口速率和存储容量的乘数。 创建后可以更改容量。 选择“1”为容量  。

   完成后，选择“下一步:  事件源”以继续进行下一步。

   [![创建时序见解环境资源](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. 现在，将该时序见解环境连接到由解决方案加速器创建的 IoT 中心。 将“选择中心”  设置为 `Select existing`。 然后，在设置“IoT 中心名称”时，选择由解决方案加速器创建的 IoT 中心  。

   [![将时序见解环境连接到已创建的 IoT 中心](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

   最后，选择“查看 + 创建”  。

1. 查看“通知”面板来监视部署进度。  

   [![时序见解环境部署成功](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>运行设备模拟

既然部署和初始配置已完成，请使用[由加速器创建的模拟设备](#create-a-device-simulation)中的数据填充时序见解环境。

除 IoT 中心以外，还生成了一个 Azure 应用服务 Web 应用程序，用来创建和传输模拟设备遥测数据。

1. 返回到[解决方案加速器仪表板](https://www.azureiotsolutions.com/Accelerators#dashboard)。 根据需要，使用本教程所用的同一 Azure 帐户重新登录。 选择“设备解决方案”，然后选择“转到解决方案加速器”  以启动部署的解决方案。

     [![解决方案加速器仪表板](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. 设备模拟 Web 应用首先会提示你授予该 Web 应用程序“登录并读取你的个人资料”的权限。 此权限使得应用程序能够检索所需的用户个人资料信息来支持运行应用程序。

     [![设备模拟 Web 应用程序许可](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. 在“模拟设置”页加载后，输入所需的参数。 

   参数|说明
   ---|---
   **目标 IoT 中心** | 选择“使用预配的 IoT 中心”。 
   **设备型号** | 选择“冷却器”。 
   **设备数目**  | 在“数量”下面输入 `1000`。 
   **遥测频率** | 输入 `10` 秒。
   **模拟持续时间** | 选择“结束时间:”并输入 `5` 分钟。 

   完成后，选择“开始模拟”  。 模拟总共会运行 5 分钟， 每隔 10 秒从 1,000 台模拟设备生成数据。 

   [![设备模拟设置](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. 运行模拟时，会看到“消息总数”和“每秒消息数”字段大约每隔 10 秒更新一次。   模拟在大约 5 分钟后结束，随后你会返回“模拟设置”。 

   [![设备模拟正在运行](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>验证遥测数据

在此最后一个部分，我们将验证时序见解环境中生成和存储的遥测数据。 若要验证数据，请使用时序见解资源管理器，它可以用于查询和分析遥测数据。

1. 返回到时序见解环境资源组的“概览”页。  选择时序见解环境。

   [![时序见解环境资源组和环境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. 在时序见解环境的“概览”页上选择“时序见解资源管理器 URL”，打开时序见解资源管理器。  

   [![时序见解资源管理器](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. 时序见解资源管理器将会加载，并使用 Azure 门户帐户进行身份验证。 初始视图出现后，可以在图表区域中看到，时序见解环境中填充了模拟的遥测数据。 若要根据更小的时间范围进行筛选，请选择左上角的下拉列表。 输入足够大的时间范围，以涵盖设备模拟的整个持续时间。 然后选择搜索放大镜。

   [![时序见解资源管理器时间范围筛选器](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 缩小时间范围可在图表中放大 IoT 中心和时序见解环境中的不同数据传输高峰期。 另请注意右上角的“流式处理完成”文本，其中显示了找到的事件的总数。  还可以拖动“间隔大小”滑块来控制图表上的绘图粒度。 

   [![时序见解资源管理器时间范围筛选视图](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 最后，也可通过左键单击某个区域来筛选范围。 然后单击右键并使用“浏览事件”，在表格式“事件”视图中显示事件详细信息。  

   [![时序见解资源管理器时间范围筛选视图和事件](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>清理资源

本教程创建了多个正常运行的 Azure 服务来支持时序见解环境和设备模拟解决方案。 若要将其删除，请导航回 Azure 门户。

在 Azure 门户左侧的菜单中执行以下操作：

1. 选择“资源组”图标。  然后，选择针对时序见解环境创建的资源组。 在页面顶部选择“删除资源组”，输入资源组的名称，然后选择“删除”。  

1. 选择“资源组”图标。  然后，选择设备模拟解决方案加速器创建的资源组。 在页面顶部选择“删除资源组”，输入资源组的名称，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建时序见解环境。
> * 创建包含 IoT 中心的设备模拟解决方案。
> * 将时序见解环境连接到 IoT 中心。
> * 运行设备模拟，将数据流式传输到时序见解环境。
> * 验证模拟的遥测数据。

了解如何创建自己的时序见解环境后，请学习如何生成可以使用时序见解环境中的数据的 Web 应用程序：

> [!div class="nextstepaction"]
> [请参阅托管客户端 SDK 可视化示例](https://tsiclientsample.azurewebsites.net/)
