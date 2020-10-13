---
title: 播放多天录制的内容 - Azure
description: 本教程介绍如何使用 Azure 媒体服务 API 播放多天连续录制的视频内容。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 1ec9260be7241057478b06446ac2aa53c14bcb47
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803426"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>教程：播放多天录制的内容  

本教程是[连续视频录制](continuous-video-recording-concept.md) (CVR) 教程的补充教程。 在本教程中，你将了解如何使用 Azure 媒体服务 API 从云端播放多天连续录制的视频内容。 

对于公共安全等方案，此功能很有用。在这些方案中，需要持续数天（或数周）用摄像机录制内容，且偶尔需要观看录制内容的特定部分。

本教程介绍如何：

> [!div class="checklist"]
> * 运行示例应用，了解如何浏览多天录制的内容
> * 查看相应录制内容的选定部分

## <a name="suggested-pre-reading"></a>建议的读前准备  

建议通读以下文档页：

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md)
* [连续视频录制](continuous-video-recording-concept.md) 
* [操作指南：播放录制内容](playback-recordings-how-to.md)
* [教程：连续视频录制](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>先决条件

* 完成 [CVR 教程](continuous-video-recording-tutorial.md)。 尽管本教程和[教程：连续视频录制](continuous-video-recording-tutorial.md)中讨论的相关 API 适用于 5 分钟或更长时间的录制，但建议录制 5 个小时甚至更长时间的视频。 长时间的录制可以最好地演示用于浏览录制内容的 API。
* 建议在[教程：连续视频录制](continuous-video-recording-tutorial.md)仍在运行的时候，运行本教程，也就是说，仍然在云中录制视频。

## <a name="run-the-sample"></a>运行示例 

[CVR 教程](continuous-video-recording-tutorial.md)期间将创建媒体服务帐户。 对于本教程，需要具有对该帐户的完整 API 访问权限。 可以按照[获取访问媒体服务 API 的凭据](../latest/access-api-howto.md?tabs=portal)中的步骤来创建服务主体。 应能够从 Azure 门户获取如下所示的 JSON 块：

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

接下来，在 Visual Studio 代码中，打开 src/ams-asset-player。 该文件夹包含本教程所需的文件。 打开 appsettings.json 文件，然后将其内容复制到新文件 appsettings.development.json 中。 对后一个文件进行以下编辑：

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="扩展设置":::
1. <!--In Visual Studio Code, you can click-->单击左侧的“运行”图标（或按 Ctrl+Shift+D），以打开要运行的可用应用程序：

    ![显示在 Visual Studio Code 中选择了运行项的菜单的屏幕截图。](./media/playback-multi-day-recordings-tutorial/run.png)
1. 从下拉框中选择 AMS 资产播放器应用程序，如下所示，然后按 F5 开始调试。

    ![屏幕截图显示在 Visual Studio Code 中选择了“AMS 资产播放器”的菜单。](./media/playback-multi-day-recordings-tutorial/debug.png)

该示例应用程序将构建并启动默认浏览器应用，然后打开 AMS 资产播放器页面。

> [!NOTE]
> 根据浏览器上的安全设置，你可能会看到一条警告消息。 由于该网页在本地运行，因此可以选择忽略该警告。

AMS 资产播放器会提示输入媒体服务资产的名称。 应使用在[教程：连续视频录制](continuous-video-recording-tutorial.md)中用于录制视频的资产的名称。

键入资产名称并点击提交后，播放器代码将加载流式处理 URL。 有关详细信息，请参阅[操作指南：播放录制内容](playback-recordings-how-to.md)。 如果按之前的建议操作，也就是当前仍在录制资产，播放器会检测到这一情况，并尝试提示播放到录制视频的最新部分。 可以在播放器的左上方看到时间戳（以 UTC 为单位）。 在下面的屏幕截图中，请注意“实时”按钮的选择方式。

![Stream](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
在播放器的右侧，可以看到用于浏览存档的控件。 此控件中的年份、月份和日期使用[操作指南：播放录制内容](playback-recordings-how-to.md)中记录的 availableMedia API 进行填充。
如果 CVR 教程已运行了数个小时，在将日期扩展后，会看到如下所示的结果：

![浏览存档文件](./media/playback-multi-day-recordings-tutorial/results.png)

本教程中的视频源的源是 MKV 文件。 RSTP 模拟器（请参阅 [Live555 媒体服务器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)）进行到文件末尾时，会结束流传输。 媒体图中的 RTSP 源节点检测到此情况并重新建立连接，然后视频继续播放。 在每次文件的结束与重新连接之间，录制的存档内容中会产生一个间隔，该间隔在 availableMedia 结果中显示为新条目。

![结果](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
单击列表中的任何一项时，应用程序都会使用相应的筛选器创建流 URL，例如 https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS)。 播放器将加载此 URL，并在预期的 startTime 时间开始播放。

或者可以通过页面底部的控件来使用特定的开始时间和结束时间。 可以参考右侧列出的 availableMedia 调用的结果，来了解可以设置哪些开始时间和结束时间值。 有关 startTime 和 endTime 筛选器的详细约束，请参阅[操作指南：播放录制内容](playback-recordings-how-to.md)。 选择时间值后，单击“提交”后，应用程序将加载播放器，其中包含流 URL，例如： https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS) 播放将从预期的 startTime 开始播放。

## <a name="next-steps"></a>后续步骤

[将基于事件的视频录制到云中并从云中播放](event-based-video-recording-tutorial.md)
