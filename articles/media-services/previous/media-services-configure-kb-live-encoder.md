---
title: 配置 Haivision KB 编码器以将单比特率实时流发送到 Azure | Microsoft Docs
description: 本主题说明如何配置 Haivision KB 实时编码器，以便将单比特率流发送到用于实时编码的 AMS 频道。
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>使用 Haivision KB 实时编码器发送单比特率实时流
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

本主题说明如何配置 [Haivision KB 实时编码器](https://www.haivision.com/products/kb-series/)，以便将单比特率流发送到用于实时编码的 AMS 频道。 有关详细信息，请参阅 [使用能够通过 Azure 媒体服务执行实时编码的频道](media-services-manage-live-encoder-enabled-channels.md)。

本教程演示了如何通过 Azure 媒体服务浏览器 (AMSE) 工具管理 Azure 媒体服务 (AMS)。 此工具仅在 Windows 电脑上运行。 如果使用的是 Mac 或 Linux，则可使用 Azure 门户创建[频道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)和[节目](media-services-portal-creating-live-encoder-enabled-channel.md)。

## <a name="prerequisites"></a>先决条件
*   访问运行 SW v5.01 或更高版本的 Haivision KB 编码器。
* [创建 Azure 媒体服务帐户](media-services-portal-create-account.md)
* 确保流式处理终结点正在运行。 有关详细信息，请参阅[在媒体服务帐户中管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)
* 安装最新版本的 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 工具。
* 启动该工具并连接到 AMS 帐户。

## <a name="tips"></a>提示
* 尽可能使用硬编码的 Internet 连接。
* 在确定带宽要求时，可以认为它就是将流式处理比特率翻倍。 虽然此要求不是强制性要求，但它可以减轻网络拥塞的影响。
* 使用基于软件的编码器时，请关闭任何不需要的程序。

## <a name="create-a-channel"></a>创建通道
1. 在 AMSE 工具中，导航到“实时”选项卡，并右键单击频道区域。 从菜单中选择“创建频道…” 从菜单中。
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. 指定频道名称，说明字段为可选字段。 在“频道设置”下针对“实时编码”选项选择“标准”，将“输入协议”设置为“RTMP”。 所有其他设置可保留原样。 确保选中“立即启动新频道”。
3. 单击“创建频道”。
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> 启动频道可能需要长达 20 分钟的时间。

## <a name="configure-the-haivision-kb-encoder"></a>配置 Haivision KB 编码器
在本教程中，将使用以下输出设置。 本部分的其余内容介绍更详细的配置步骤。

视频：
-   编解码器：H.264
-   配置文件：高（等级 4.0）
-   比特率：5000 kbps
-   关键帧：2 秒（60 秒）
-   帧速率：30

音频：
-   编码解码器：AAC (LC)
-   比特率：192 kbps
-   采样速率：44.1 kHz

## <a name="configuration-steps"></a>配置步骤
1.  登录到 Haivision KB 用户界面。
2.  单击频道控制中心的“菜单按钮”，然后选择“添加频道”  
    ![Screen Shot 2017-08-14 at 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  在“名称”字段中键入“频道名”，然后单击“下一步”。  
    ![Screen Shot 2017-08-14 at 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  从“输入源”下拉列表中选择“频道输入源”，然后单击“下一步”。
    ![Screen Shot 2017-08-14 at 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  从“编码器模板”下拉列表中选择“H264-720-AAC-192”，然后单击“下一步”。
    ![Screen Shot 2017-08-14 at 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  从“选择新输出”下拉列表中选择“RTMP”，然后单击“下一步”。  
    ![Screen Shot 2017-08-14 at 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  在“频道输出”窗口，填充 Azure 流信息。 粘贴“服务器”区域中初始频道设置中的 RTMP 链接。 在“输出名”区域键入频道名。 在“流名称模板”区域中，使用模板 RTMPStreamName_％video_bitrate％ 命名流。
    ![Screen Shot 2017-08-14 at 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  单击“下一步”，然后单击“完成”。
9.  单击“播放按钮”启动编码器频道。  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>测试播放
导航回 AMSE 工具，并右键单击要测试的频道。 在菜单中，将鼠标悬停在“播放预览”上方，然后选择“使用 Azure 媒体播放器”。

如果流出现在播放器中，则编码器已正确配置，可以连接到 AMS。

如果收到错误，则需重置频道并调整编码器设置。 有关指南，请参阅《疑难解答》一文。

## <a name="create-a-program"></a>创建节目
1.  一旦确认频道可以播放，则可创建节目。 在 AMSE 工具的“实时”选项卡下，右键单击节目区域，并选择“创建新节目”。
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  为节目命名，并根据需要调整“存档时段长度”（默认为 4 小时）。 还可以指定存储位置，也可以将其保留为默认值。
2.  选中“立即启动节目”框。
3.  单击“创建节目”。
4.  运行节目以后，可通过下述方式来确认其是否能够播放：右键单击该节目，导航到“播放节目”，并选择“使用 Azure Media Player”。
5.  确认以后，再次右键单击该节目，然后选择“将输出 URL 复制到剪贴板”（也可通过菜单从“节目信息和设置”选项检索此信息）。

现在可以将流嵌入到播放器中，也可将其分发给受众进行实时观看。

> [!NOTE]
> 创建节目需要的时间比创建频道需要的时间少。