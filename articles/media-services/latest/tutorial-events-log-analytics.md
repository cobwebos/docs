---
title: 将 Azure 媒体服务事件存储在 Azure Log Analytics 中
titleSuffix: Azure Media Services
description: 了解如何将 Azure 媒体服务事件存储在 Azure Log Analytics 中。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: cc3060c9253b23b97089ea35625aceb26737baba
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016989"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>教程：将 Azure 媒体服务事件存储在 Azure Log Analytics 中

## <a name="azure-media-services-events"></a>Azure 媒体服务事件

Azure 媒体服务 v3 在 [Azure 事件网格](media-services-event-schemas.md)上发出事件。 你可以通过多种方式订阅事件并将其存储在数据存储中。 在本教程中，你将使用[逻辑应用流](https://azure.microsoft.com/services/logic-apps/)订阅媒体服务事件。 系统将为每个事件触发逻辑应用，并将事件的主体存储在 Azure Log Analytics 中。 一旦将事件存储到 Azure Log Analytics 中，就可以使用其他 Azure 服务来创建仪表板、监视这些事件并针对其发出警报，但本教程不会对此进行介绍。

> [!NOTE]
> 如果你已经熟悉如何使用 FFmpeg 作为本地编码器，这将很有帮助。  如果不熟悉，也没关系。 下面包含了用于流式传输视频的命令行和说明。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建无代码逻辑应用流
> * 订阅 Azure 媒体服务事件主题
> * 分析事件并存储到 Azure Log Analytics
> * 从 Azure Log Analytics 查询事件

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

> * [Azure 订阅](how-to-set-azure-subscription.md)
> * [媒体服务](create-account-howto.md)帐户和资源组。
> * 根据 OS 安装 [FFmpeg](https://ffmpeg.org/download.html)。
> * [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) 工作区

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>使用逻辑应用订阅媒体服务事件

1. 在 Azure 门户中，如果尚未执行此操作，则创建一个 [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) 工作区。 你需要工作区 ID 和其中一个密钥，因此请使该浏览器窗口保持打开状态。 然后，在另一个选项卡或窗口中打开门户。

1. 导航到 Azure 媒体服务帐户，并选择“事件”。 这将显示订阅 Azure 媒体服务事件的所有方法。
    > [!div class="mx-imgBorder"]
    > ![Azure 媒体服务门户](media/tutorial-events-log-analytics/select-events-01a.png)

1. 选择逻辑应用图标以创建逻辑应用。 这将打开逻辑应用设计器，你可以在其中创建流来捕获事件，并将其推送到 Log Analytics。 
    > [!div class="mx-imgBorder"]
    > ![创建逻辑应用](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. 选择 + 图标，选择要使用的租户，然后选择“登录”。 你将看到 Microsoft 登录提示。
    > [!div class="mx-imgBorder"]
    > ![连接到 Azure 事件网格](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![选择租户](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. 选择“继续”以订阅媒体服务事件。
    > [!div class="mx-imgBorder"]
    > ![已连接到 Azure 事件网格](media/tutorial-events-log-analytics/select-continue-04.png)

1. 在“资源类型”列表中，找到“Microsoft.Media.MediaServices”。
    > [!div class="mx-imgBorder"]
    >![Azure 媒体服务资源事件](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. 选择“事件类型项”。 此时将列出 Azure 媒体服务发出的所有事件。 可以选择要跟踪的事件。可以添加多个事件类型。 （稍后将对逻辑应用流进行一些小的更改，以将每个事件类型存储在单独的 Log Analytics 日志中，并将事件类型名称动态传播到 Log Analytics 日志名称。）
    > [!div class="mx-imgBorder"]
    > ![Azure 媒体服务事件类型](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. 选择“另存为”。

1. 为逻辑应用命名。  默认情况下，资源组处于选中状态。 其他设置保持不变，然后选择“创建”。  你将返回到 Azure 主屏幕。
    > [!div class="mx-imgBorder"]
    > ![逻辑应用命名界面](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>创建操作

你已订阅事件，接下来创建操作。

1. 如果门户已将你带回主屏幕，请导航回到刚刚创建的逻辑应用，方法是在“所有资源”中搜索应用名称。

1. 选择应用，然后选择“逻辑应用设计器”。 此时将打开设计器窗格。

1. 选择“+ 新步骤”。

1. 由于要将事件推送到 Azure Log Analytics 服务，因此请搜索“Azure Log Analytics”并选择“Azure Log Analytics 数据收集器”。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 数据收集器](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. 若要连接到 Log Analytics 工作区，你需要提供工作区 ID 和代理密钥。 在新选项卡或窗口中打开 Azure 门户，导航到在本教程开始之前创建的 Log Analytics 工作区。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 工作区 ID](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. 在左侧菜单中，找到“代理管理”并选中它。 这将显示已生成的代理密钥。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 代理管理](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. 复制工作区 ID。
    > [!div class="mx-imgBorder"]
    > ![复制工作区 ID](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. 在另一个浏览器选项卡或窗口中的 Azure Log Analytics 数据收集器下，选择“发送数据”，为连接命名，然后将工作区 ID 粘贴到“工作区 ID”字段中。

1. 返回到“工作区”浏览器选项卡或窗口，然后复制工作区密钥。
    > [!div class="mx-imgBorder"]
    > ![代理管理主密钥](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. 在另一个浏览器选项卡或窗口中，将工作区密钥粘贴到“工作区密钥”字段中。

1. 选择“创建”  。 现在，你将创建 JSON 请求正文和自定义日志名称。

1. 选择“JSON 请求正文”字段。  此时将显示指向“添加动态内容”的链接。

1. 选择“添加动态内容”，然后选择“主题”。

1. 对“自定义日志名称”执行相同的操作。
    > [!div class="mx-imgBorder"]
    > ![已选择主题](media/tutorial-events-log-analytics/topic-selected.png)

1. 选择逻辑应用的代码视图。 查找“Inputs”和“Log-Type”行。
    > [!div class="mx-imgBorder"]
    > ![两行代码视图](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. 将 `body` 的值从 `"@triggerBody()?['topic']"` 更改为 `"@{triggerBody()}"`。 这是为了向 Log Analytics 分析整条消息。

1. 将 `Log-Type` 从 `"@triggerBody()?['topic']"` 更改为 `"@replace(triggerBody()?['eventType'],'.','')"`。 （这将替换“.”，因为它们不能出现在 Log Analytics 日志名称中。）
    > [!div class="mx-imgBorder"]
    > ![更改后的逻辑应用 json](media/tutorial-events-log-analytics/changed-lines.png)

1. 选择“保存” 。

1. 若要进行验证，请选择“逻辑应用设计器”。
    > [!div class="mx-imgBorder"]
    > ![验证正文和函数步骤](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. 当你检查资源组中的所有资源时，系统会列出一个逻辑应用和两个逻辑应用 API 连接器（一个用于事件，一个用于 Log Analytics）。 有关事件网格系统主题的详细信息，请阅读[事件网格系统主题](../../event-grid/system-topics.md)。
    > [!div class="mx-imgBorder"]
    > ![查看资源组中的所有新资源](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>测试

若要测试其实际工作方式，请在 Azure 媒体服务中创建一个实时事件。 创建 RTMP 实时事件，并使用 ffmpeg 基于 .mp4 示例文件推送“实时”流。 创建事件后，获取 RTMP 引入 URL。

1. 从媒体服务帐户中选择“实时传送视频流”。
    > [!div class="mx-imgBorder"]
    > ![创建 Azure 媒体服务实时事件](media/tutorial-events-log-analytics/live-event.png)

1. 选择“添加实时事件”。

1. 在“实时事件名称”字段中输入名称。 （“说明”字段是可选的。）

1. 选择“标准”云编码。

1. 选择“默认 720p”作为编码预设。

1. 选择“RTMP”输入协议。

1. 为持久性输入 URL 选择“是”。

1. 创建事件后，选择“是”以启动事件。

    > [!WARNING]
    > 如果选择“是”，系统将开始计费。  如果你想等到使用 FFmpeg 开始进行流式传输的前一刻再启动流，请选择“否”，并记得启动实时事件。

    > [!div class="mx-imgBorder"]
    > ![实时事件设置](media/tutorial-events-log-analytics/live-event-settings.png)

1. 选中“我拥有使用内容/文件所需的全部权限...”复选框。

1. 选择“查看 + 创建”。

1. 查看设置，然后选择“创建”。  此时将显示实时事件列表，并显示实时事件引入 URL。

1. 将“引入 URL”复制到剪贴板。

1. 选择列表中的“实时事件”以查看创建器视图。

### <a name="stream-with-ffmpeg-cli"></a>使用 FFmpeg CLI 进行流式传输

1. 使用以下命令行。

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. 将 `<ingestURL>` 更改为复制到剪贴板的引入 URL。
1. 将 `<localpathtovideo>` 更改为要从 FFmpeg 流式传输的文件的本地路径。
1. 在末尾添加一个唯一名称，例如 `mystream`。
1. 调整命令行以反映测试源文件和任何其他系统变量。
1. 运行命令。 几秒钟后，“创建器视图”播放器应启动流式传输。 （如果视频未自动显示，请刷新播放器。）

    > [!div class="mx-imgBorder"]
    > ![验证创建器视图播放器中的视频引入是否正确](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>验证事件

在实时流中，Azure 媒体服务会发出各种触发逻辑应用流的事件。 若要进行验证，请导航到逻辑应用，并确定媒体服务中的事件是否触发了任何触发器。

1. 导航到逻辑应用概述页，应会看到“运行历史记录”列出了已成功完成的作业。
    > [!div class="mx-imgBorder"]
    > ![验证逻辑应用中的作业执行是否成功](media/tutorial-events-log-analytics/run-history.png)

1. 选择一个成功的作业。 系统将显示运行时作业的详细信息。
1. 选择“发送数据”将其展开。 在本例中，`MicrosoftMediaLiveEventEncoderConnected` 事件显示它已被捕获，并显示分析后的正文。 这是推送到 Azure Log Analytics 工作区的内容。
    > [!div class="mx-imgBorder"]
    > ![查看“发送数据”](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>验证日志

1. 导航到先前创建的 Log Analytics 工作区。

1. 选择“日志”。 
1. 关闭“示例查询”弹出窗口。
1. 将出现一个“自定义日志”列表。 选择向下箭头将其展开。 在这里，你将看到事件名称 `MicrosoftMediaLiveEventEncoderConnected`。
1. 选择该事件名称将其展开。
1. 选择“眼睛”图标时，它将显示查询结果的预览。
1. 选择“在查询编辑器中查看”，然后选择“TimeGenerated UTC”列表下的项，以将其展开并查看原始数据。

![在 Log Analytics 中查看详细的事件输出](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>删除资源

如果不想继续使用在本教程中创建的资源，请确保删除资源组中的所有资源，否则系统将继续收费。

## <a name="next-steps"></a>后续步骤

可以创建不同的查询并保存它们。 可以将它们添加到 [Azure 仪表板](../../azure-monitor/learn/tutorial-logs-dashboards.md)中。