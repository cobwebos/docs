---
title: "如何向 Azure 时序见解环境添加 IoT 中心事件源 | Microsoft 文档"
description: "本文介绍如何将已连接至 IoT 中心的事件源添加到时序见解环境"
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: ed31a0e725d1e0863e9c4695d4eccb324f60678a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>如何向 Azure 时序见解环境添加 IoT 中心事件源
本文介绍如何使用 Azure 门户将从 IoT 中心读取数据的事件源添加到时序见解环境。

## <a name="prerequisites"></a>先决条件
- 创建时序见解环境。 有关详细信息，请参阅[创建 Azure 时序见解环境](time-series-insights-get-started.md) 
- 创建 IoT 中心。 有关 IoT 中心的详细信息，请参阅[使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
- IoT 中心需要有正在发送进来的活动消息事件。
- 在 IoT 中心创建专用使用者组，以供时序见解环境使用。 每个时序见解事件源都需要具有自己的专用使用者组，该组不与其他使用者共享。 如果多个读取器使用同一使用者组中的事件，则所有读取器都可能出现故障。 有关详细信息，请参阅 [IoT 中心开发人员指南](../iot-hub/iot-hub-devguide.md)。

## <a name="add-a-new-event-source"></a>添加新的事件源
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 定位现有的时序见解环境。 在 Azure 门户左侧的菜单中，单击“所有资源”。 选择时序见解环境。

3. 在“环境拓扑”标题下，单击“事件源”。
   事件源 + 添加![](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. 单击“+ 添加”。

5. 提供专属于此时间见解环境的“事件源名称”，如“事件流”。

   ![填写表单中的前三个参数。](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. 选择“源”作为“IoT 中心”。

7. 选择适当的“导入”选项。 
   - 当你在其中一个订阅上已有现有的 IoT 中心时，请选择“从可用订阅使用 IoT 中心”。 这是最简单的方法。
   - 当 IoT 中心在你的订阅外部，或者你希望选择高级选项时，请选择“手动提供 IoT 中心设置”。 

8. 如果选择了“通过可用订阅使用 IoT 中心”选项，下表说明了每个所需的属性：

   ![订阅和事件中心详细信息](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | 属性 | 说明 |
   | --- | --- |
   | 订阅 ID | 选择其中创建有此 IoT 中心的订阅。
   | IoT 中心名称 | 选择 IoT 中心的名称。
   | IoT 中心策略名称 | 选择共享访问策略，可在 IoT 中心设置选项卡中找到此策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有服务连接权限。
   | IoT 中心策略密钥 | 密钥已预填充。
   | IoT 中心使用者组 | 从 IoT 中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。
   | 事件序列化格式 | JSON 是目前唯一可用的序列化。 事件消息必须采用此格式，否则将无法读取任何数据。 |
   | 时间戳属性名称 | 若要确定此值，你需要了解发送到 IoT 中心的消息数据的消息格式。 此值是消息数据中你想要用作事件时间戳的特定事件属性的“名称”。 该值区分大小写。 如果留空，则事件源中的“事件排队时间”将用作事件时间戳。 |

9. 如果选择了“手动提供 IoT 中心设置”选项，下表说明了每个所需的属性：

   | 属性 | 说明 |
   | --- | --- |
   | 订阅 ID | 在其中创建此 IoT 中心的订阅。
   | 资源组 | 在其中创建此 IoT 中心的资源组名称。
   | IoT 中心名称 | IoT 中心的名称。 创建 IoT 中心时，也为它提供了一个特定的名称。
   | IoT 中心策略名称 | 共享访问策略，可在 IoT 中心设置选项卡中创建此策略。每个共享访问策略具有名称、所设权限以及访问密钥。 事件源的共享访问策略必须具有服务连接权限。
   | IoT 中心策略密钥 | 用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 在此处键入主密钥或辅助密钥。
   | IoT 中心使用者组 | 从 IoT 中心读取事件的使用者组。 强烈建议为事件源使用专用的使用者组。
   | 事件序列化格式 | JSON 是目前唯一可用的序列化。 事件消息必须采用此格式，否则将无法读取任何数据。 |
   | 时间戳属性名称 | 若要确定此值，你需要了解发送到 IoT 中心的消息数据的消息格式。 此值是消息数据中你想要用作事件时间戳的特定事件属性的“名称”。 该值区分大小写。 如果留空，则事件源中的“事件排队时间”将用作事件时间戳。 |

10. 选择“创建”以添加新的事件源。

   ![单击创建](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   创建事件源以后，时序见解就会自动将数据流式传输到你的环境中。

### <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心
应用程序使用使用者组来从 Azure IoT 中心拉取数据。 提供专用使用者组，仅供此时序见解环境使用，以可靠地从 IoT 中心读取数据。

要将新使用者组添加到 IoT 中心，请执行以下步骤：
1. 在 Azure 门户中，找到并打开 IoT 中心。

2. 在“消息”标题下，选择“终结点”。 

   ![添加使用者组](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. 选择“事件”终结点，“属性”页将打开。

4. 在“使用者组”标题下，为使用者组提供新的唯一名称。 创建新的事件源时，请在时序见解环境中使用此相同名称。

5. 选择**保存**以保存新的使用者组。

## <a name="next-steps"></a>后续步骤
- [定义数据访问策略](time-series-insights-data-access.md)，以保护数据。
- [发送事件](time-series-insights-send-events.md)到事件源。
- 在[时序见解资源管理器中访问环境](https://insights.timeseries.azure.com)。
