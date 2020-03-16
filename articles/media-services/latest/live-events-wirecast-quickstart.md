---
title: 使用门户和 Wirecast 创建 Azure 媒体服务实时流
description: 了解如何创建 Azure 媒体服务实时流
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927570"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>使用门户和 Wirecast 创建 Azure 媒体服务实时流

本入门指南假设你有一个 Azure 订阅，并已创建 Azure 媒体服务帐户。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

打开 Web 浏览器，导航到 [Microsoft Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

本快速入门中将介绍：

- 使用免费试用版 Telestream Wirecast 设置本地编码器
- 设置实时流
- 设置实时流输出
- 运行默认的流式处理终结点
- 使用 Azure Media Player 观看实时流和按需输出

为简单起见，我们将为 Wirecast、直通云编码和 RTMP 中的 Azure 媒体服务使用一个编码预设。

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>使用 Wirecast 设置本地编码器

1. 从 https://www.telestream.net 下载适用于你的操作系统的 Wirecast 并进行安装
1. 启动该应用程序，并使用常用的电子邮件地址注册产品。  将应用程序保持打开状态。
1. 你将收到一封要求验证电子邮件地址的电子邮件，然后，该应用程序会启动免费试用版。
1. 建议：在打开的应用程序屏幕中观看视频教程。

## <a name="setting-up-an-azure-media-services-live-stream"></a>设置 Azure 媒体服务实时流

1. 在门户中导航到 Azure 媒体服务帐户后，从媒体服务列表中选择“实时传送视频流”。 
1. 单击“添加实时事件”创建新的实时传送视频流事件。 
1. 在实时事件的“名称”字段中，输入新事件的名称，例如 *TestLiveEvent*。 
1. 在“说明”字段中输入事件的可选说明。 
1. 选中“直通 – 无云编码”单选按钮。 
1. 选中“RTMP”单选按钮。  
1. 确保选中“启动实时事件”对应的“无”单选按钮，以免在该实时事件准备就绪之前产生费用。   （一旦启动实时事件，就会开始计费。）
1. 单击“查看 + 创建”按钮检查设置。 
1. 单击“创建”按钮以创建实时事件。  随后你会返回到实时事件列表视图。
1. 单击刚刚创建的**实时事件的链接**。 可以观察到你的事件已停止。
1. 在浏览器中将此页保持打开状态。  稍后我们将返回此页。

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>使用 Wirecast Studio 设置实时流

1. 假设 Wirecast 应用程序仍保持打开状态。在主菜单中选择“创建空文档”，然后单击“继续”。  
1. 将鼠标悬停在“Wirecast 层”区域中的第一个层上。  单击出现的“添加”图标，然后选择要流式传输的视频输入。   此时会打开“主层 1”对话框。
1. 在菜单中选择“视频捕获”，然后选择要使用的相机。  相机中的视图将显示在预览区域中。
1. 将鼠标悬停在“Wirecast 层”区域中的第二个层上。 单击出现的“添加”图标，然后选择要流式传输的音频输入。   此时会打开“主层 2”对话框。
1. 在菜单中选择“音频捕获”，然后选择要使用的音频输入。  
1. 在主菜单中选择“输出设置”。   此时会显示“输出”对话框。
1. 从输出下拉列表中选择“Azure 媒体服务”。   Azure 媒体服务的输出设置会自动填充大部分输出设置。 
1. 在下一部分，你将在浏览器中返回到 Azure 媒体服务来复制“输入 URL”，以便将其输入到输出设置中。 

### <a name="copy-and-paste-the-input-url"></a>复制并粘贴输入 URL

1. 返回到门户的“Azure 媒体服务”页，单击“启动”以启动实时流事件。  （现在开始计费。）
2. 单击“安全/不安全”切换开关，将其设置为“不安全”。    这会将协议设置为 RTMP 而不是 RTMPS。
3. 将“输入 URL”复制到剪贴板。 
4. 切换到 Wirecast 应用程序，将“输入 URL”粘贴到输出设置中的“地址”字段。  
5. 单击“确定”。 

## <a name="setting-up-outputs"></a>设置输出

此部分将设置输出，并使你可以保存实时流的录制内容。  

> [!NOTE]
> 若要流式传输此输出，流式处理终结点必须正在运行。  请参阅下面的“运行默认流式处理终结点”部分。

1. 单击“输出视频查看器”下面的“创建输出”链接。 
1. 如果需要，请将“名称”字段中的输出名称编辑为用户更容易理解的内容，以便于今后查找。 
1. 暂时不要理会其他所有字段。
1. 单击“下一步”添加流定位器。 
1. 如果需要，请将定位器的名称更改为用户更容易理解的内容。
1. 暂时不要理会此屏幕上的其他任何设置。
1. 单击“创建”。 

## <a name="starting-the-broadcast"></a>开始广播

1. 在 Wirecast 中，从主菜单中选择“输出”>“开始/停止广播”>“启动 Azure 媒体服务:  Azure 媒体服务”。  将流发送到实时事件后，Wirecast 中的“实时”窗口会显示在 Azure 媒体服务中实时事件页上的实时事件视频播放器中。

1. 单击预览窗口下面的“开始”按钮，以开始广播已为 Wirecast 层选择的视频和音频。 

> [!TIP]
> 如果出错，请单击播放器上方的“重新加载播放器”链接来尝试重新加载播放器。

## <a name="running-the-default-streaming-endpoint"></a>运行默认的流式处理终结点

1. 在媒体服务列表中选择“流式处理终结点”，以确保流式处理终结点正在运行。  随后会转到流式处理终结点页。
1. 如果默认流式处理终结点的状态为“已停止”，请单击**默认**流式处理终结点。 随后会转到该终结点的页面。
1. 单击“启动”  。  随即会启动该流式处理终结点。

## <a name="play-the-output-broadcast-with-azure-media-player"></a>使用 Azure Media Player 播放输出的广播

1. 复制“输出视频播放器”下面的“流 URL”。  
1. 在 Web 浏览器中，打开 Azure Media Player 演示 https://ampdemo.azureedge.net/azuremediaplayer.html
1. 将“流 URL”粘贴到 Azure Media Player 的“URL”字段中。 
1. 单击“更新播放器”按钮。 
1. 单击视频中的“播放”图标查看实时流。 

## <a name="stopping-the-broadcast"></a>停止广播

如果你认为已流式传输了足够的内容，请停止广播。

1. 在 Wirecast 中单击“广播”按钮。   随即会在 Wirecast 中停止广播。
1. 在门户中单击“停止”。  此时会出现一条警告消息，指出实时流将会停止，但输出现在会变为按需资产。
1. 单击警告消息中的“停止”。  Azure Media Player 现在还会显示错误，因为实时流不再可用。

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>使用 Azure Media Player 播放按需输出

现在，只要流式处理终结点正在运行，创建的输出就可供按需流式处理。

1. 导航到媒体服务列表并选择“资产”。 
1. 找到前面创建的事件输出，然后单击“链接到资产”。  此时会打开资产输出页。
1. 在资产的视频播放器下复制“流 URL”。 
1. 在浏览器中返回到 Azure Media Player，将“流 URL”粘贴到 Azure Media Player 的“URL”字段中。 
1. 单击“更新播放器”。 
1. 单击视频中的“播放”图标以查看按需资产。 

## <a name="clean-up-resources"></a>清理资源

  > [!IMPORTANT]
  > 请停止服务！ 完成本快速入门中的步骤后，请务必停止实时事件和流式处理终结点，否则系统会按它们的运行时间继续计费。 若要停止实时事件，请参阅上述“停止广播”部分的步骤 2 和 3。

### <a name="stopping-the-streaming-endpoint"></a>停止流式处理终结点

1. 从媒体服务列表中选择“流式处理终结点”。 
2. 单击前面启动的**默认**流式处理终结点。 此时会打开该终结点的页面。
3. 单击“停止”。   随即会停止该流式处理终结点。

>[!TIP]
>如果你不想要保留来自此事件的资产，请务必将其删除，以免产生存储费用。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [接下来的文章](./live-events-outputs-concept.md)
