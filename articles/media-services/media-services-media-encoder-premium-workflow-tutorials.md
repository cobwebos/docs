---
title: "高级媒体编码器高级工作流教程"
description: "本文档包含的演练说明如何使用媒体编码器高级工作流执行高级任务，以及如何使用工作流设计器创建复杂的工作流。"
services: media-services
documentationcenter: 
author: xstof
manager: cfowler
editor: 
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 565497bd5a35e3c4d69d29512307cf3ca2364bdd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>高级媒体编码器高级工作流教程
## <a name="overview"></a>概述
本文档包含的演练说明如何使用**工作流设计器**自定义工作流。 可以在[此处](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)找到实际的工作流文件。  

## <a name="toc"></a>目录
本文涵盖以下主题：

* [将 MXF 编码为单比特率 MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [启动新工作流](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [使用媒体文件输入](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [检查媒体流](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [添加视频编码器用于生成 .MP4 文件](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [为音频流编码](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [将音频和视频流多路复用到 MP4 容器](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [写入 MP4 文件](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [从输出文件创建媒体服务资产](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [在本地测试完成的工作流](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [将 MXF 编码为多比特率 MP4 - 动态打包已启用](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [添加一个或多个其他 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [配置文件输出名称](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [添加单独的音轨](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [添加 .ISM SMIL 文件](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [将 MXF 编码为多比特率 MP4 - 增强的蓝图](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [要增强的工作流概述](#workflow-overview-to-enhance)
  * [文件命名约定](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [将组件属性发布到工作流根目录](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [使生成的输出文件名依赖于发布的属性值](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [将缩略图添加到多比特率 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [要将缩略图添加到的工作流概述](#workflow-overview-to-add-thumbnails-to)
  * [添加 JPG 编码](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [处理颜色空间转换](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [写入缩略图](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [在工作流中检测错误](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [已完成的工作流](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [对多比特率 MP4 输出进行基于时间的修剪](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [要在其中开始添加修剪的工作流概述](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [使用流修剪器](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [已完成的工作流](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [引入脚本组件](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [在工作流中编写脚本：hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [对多比特率 MP4 输出进行基于帧的修剪](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [要在其中开始添加修剪的蓝图概述](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [使用剪辑列表 XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [通过脚本组件修改剪辑列表](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [添加 ClippingEnabled 便利属性](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>将 MXF 编码为单比特率 MP4
在本演练中，我们使用来自 .MXF 输入文件 AAC-HE 编码的音频来创建单比特率 .MP4 文件。

### <a id="MXF_to_MP4_start_new"></a>启动新工作流
打开“工作流设计器”，并选择“文件”-“新建工作区”-“转码蓝图”

新的工作流显示 3 个元素：

* 主源文件
* 剪辑列表 XML
* 输出文件/资产  

![新编码工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

新编码工作流

### <a id="MXF_to_MP4_with_file_input"></a>使用媒体文件输入
为了接受我们的输入媒体文件，请从添加媒体文件输入组件开始。 如果要将组件添加到工作流，请在“存储库”搜索框中查找它，并将所需的项拖放到设计器窗格。 对“媒体文件输入”执行此操作，并将“主源文件”组件从“媒体文件输入”连接到“文件名”输入插针。

![连接的媒体文件输入](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

连接的媒体文件输入

在可以执行更多操作之前，我们需要先向工作流设计器指出我们要用于设计工作流的示例文件。 为此，请单击设计器窗格背景，并在右侧属性窗格中找到“主源文件”属性。 单击文件夹图标，并选择所需的文件来测试工作流。 完成此操作之后，媒体文件输入组件会检查文件，并填充其输出插针，以反映它检查的文件。

![填充的媒体文件输入](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

填充的媒体文件输入

尽管这指定我们想要使用的输入，但并未告知编码的输出应该通往的位置。 现在，请使用与配置主源文件类似的方式，紧靠在主源文件的下面配置“输出文件夹变量”属性。

![配置的输入和输出属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

配置的输入和输出属性

### <a id="MXF_to_MP4_streams"></a>检查媒体流
通常我们想要知道经过工作流之后流的外观。 若要在工作流中的任一时间点检查流，只需单击任何组件上的输出或输入插针。 在此情况下，请尝试从我们的“媒体文件输入”单击“未压缩的视频”输出插针。 此时会打开一个对话框，让你检查输出视频。

![检查未压缩的视频输出插针](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

检查未压缩的视频输出插针

在本例中，它告诉我们要对一段接近 2 分钟的视频以 4:2:2 采样率、每秒 24 帧的速率处理 1920x1080 输入。

### <a id="MXF_to_MP4_file_generation"></a>添加视频编码器用于生成 .MP4 文件
请注意，现在，“未压缩的视频”和多个“未压缩的音频”输出插针可供用于我们的媒体文件输入。 若要为输入视频编码，我们需要一个编码组件 - 在此情况下用于生成 .MP4 文件。

要将视频流编码成 H.264，请将 AVC 视频编码器组件添加到设计器图面。 此组件将未压缩的视频流作为输入，并在其输出插针上提供 AVC 压缩视频流。

![未连接的 AVC 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

未连接的 AVC 编码器

其属性决定编码的确切发生方式。 让我们看一些更重要的设置：

* 输出宽度和输出高度：这些属性决定编码视频的分辨率。 在本例中，我们使用 640x360
* 帧速率：设置为通过时，它只采用源帧速率，不过可以覆盖。 请注意，这种帧速率转换并未经过运动补偿。
* 配置文件和级别：这些属性决定 AVC 配置文件和级别。 要方利地获取不同级别和配置文件的详细信息，请单击“AVC 视频编码器”组件中的问号图标，帮助页面会显示有关每个级别的详细信息。 在本例中，让我们对“主配置文件”使用级别 3.2（默认值）。
* 速率控制模式和比特率 (kbps)：在本例中，我们选择使用 1200 kbps 恒定比特率 (CBR) 输出
* 视频格式：这是关于写入 H.264 流的 VUI（视频可用性信息）（编解码器可能用于增强显示、但对正确编解码并非必需的辅助信息）：
* NTSC（一般用于美国或日本，使用 30 fps）
* PAL（一般用于欧洲地区，使用 25 fps）
* GOP 大小模式：根据我们的目的，我们将配置固定的 GOP 大小，主要间隔为 2 秒，并关闭 GOP。 这可确保与 Azure 媒体服务提供的动态打包兼容。

要馈送我们的 AVC 编码器，请将“未压缩的视频”输出插针从“媒体文件输入”组件连接到“AVC 编码器”的“未压缩的视频”输入插针。

![连接的 AVC 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

连接的 AVC 主编码器

### <a id="MXF_to_MP4_audio"></a>为音频流编码
此时，我们已将视频编码，但仍需要压缩原始未压缩的音频流。 对此，我们将使用 AAC 编码器 (Dolby) 组件进行 AAC 编码。 将该组件添加到工作流。

![未连接的 AVC 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

未连接的 AAC 编码器

目前不兼容性：AAC 编码器只有单个未压缩音频输入插针，而媒体文件输入可能有两个不同的未压缩音频流可用：一个用于左音频声道，一个用于右声道。 （如果正在处理环绕音效，则有 6 个声道。）因此，无法直接将音频从“媒体文件输入”源连接到 AAC 音频编码器。 AAC 组件预期需要一个称为“交错”的音频流：具有左右声道并彼此交错的单一流。 一旦我们从源媒体文件知道哪一个音频数据轨在源中的哪个位置，我们可以使用正确分配的左右扬声器位置来生成此类交错音频流。

首先，用户想要从所需的源音频声道生成交错流。 音频流交错器组件将为我们处理。 将它添加到工作流，并从“媒体文件输入”将音频输出连接到它。

![连接的音频流交错器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

连接的音频流交错器

我们已有交错的音频流，但仍未分配左右扬声器的位置。 为了指定位置，我们可以利用“扬声器位置分配器”。

![添加扬声器位置分配器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

添加扬声器位置分配器

配置“扬声器位置分配器”以配合使用通过“自定义”编码器预设过滤器和称为“2.0 (L,R)”的声道预设的立体声输入流。 （这会将左扬声器位置分配为声道 1，将右扬声器位置分配为声道 2。）

将“扬声器位置分配器”的输出连接到“AAC 编码器”的输入。 然后，告诉 AAC 编码器使用“2.0 (L,R)”声道预设，让它知道要将立体声音频处理为输入。

### <a id="MXF_to_MP4_audio_and_fideo"></a>将音频和视频流多路复用到 MP4 容器
假设我们有 AVC 编码的视频流和 AAC 编码的音频流，我们可以将两者捕获为 .MP4 容器。 将不同的流混合为单一流的过程称为“多路复用”（或“muxing”）。 在本例中，我们正在将音频和视频流交错到单个一致的 .MP4 包。 为 .MP4 容器协调此操作的组件称为 ISO MPEG-4 多路复用器。 将一个多路复用器添加到设计器图面，并将 AVC 视频编码器和 AAC 编码器连接到其输入。

![连接的 MPEG4 多路复用器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

连接的 MPEG4 多路复用器

### <a id="MXF_to_MP4_writing_mp4"></a>写入 MP4 文件
写入输出文件时，使用“文件输出”组件。 我们可以将它连接到 ISO MPEG-4 多路复用器的输出，让其将输出写入到磁盘。 为此，请将容器 (MPEG-4) 输出插针连接到“文件输出”的“写入”输入插针。

![连接的文件输出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

连接的文件输出

要使用的文件名取决于“文件”属性。 尽管可以将该属性硬编码为给定值，但用户很可能想要改为通过表达式设置它。

若要让工作流通过表达式自动判断输出“文件名”属性，请单击“文件名”旁边的按钮（文件夹图标旁）。 从下拉菜单中选择“表达式”。 这会显示表达式编辑器。 先清除编辑器的内容。

![空白的表达式编辑器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

空白的表达式编辑器

表达式编辑器允许以一个或多个变量的组合输入任何文本值。 以货币符号开头的变量。 当你按 $ 键时，编辑器会显示一个下拉框，其中包含一系列可用变量。 在此例中，我们使用输出目录变量与基本输入文件名变量的组合：

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![填充的表达式编辑器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

填充的表达式编辑器

> [!NOTE]
> 若要在 Azure 中查看编码作业的输出文件，必须在表达式编辑器中提供值。
>
>

按“确定”确认表达式时，“属性”窗口提供在此时间点的文件属性所解析的值预览。

![文件表达式解析输出目录](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

文件表达式解析输出目录

### <a id="MXF_to_MP4_asset_from_output"></a>从输出文件创建媒体服务资产
尽管我们已编写一个 MP4 输出文件，我们仍需要指出此文件属于媒体服务因为执行此工作流生成的输出资产。 为此，请使用工作流画布上的“输出文件/资产”节点。 所有传入到此节点的文件成为生成的 Azure 媒体服务资产的一部分。

将“文件输出”组件连接到“输出文件/资产”组件以完成工作流。

![已完成的工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*已完成的工作流*

### <a id="MXF_to_MP4_test"></a>在本地测试完成的工作流
若要在本地测试工作流，请按顶部工具栏中的播放按钮。 工作流完成执行后，请检查配置的输出文件夹中生成的输出。 会看到从 MXF 输入源文件编码完成的 MP4 输出文件。

## <a id="MXF_to_MP4_with_dyn_packaging"></a>将 MXF 编码为多比特率 MP4 - 动态打包已启用
在本演练中，我们使用来自单个 .MXF 输入文件 AAC 编码的音频来创建一组多比特率 MP4 文件。

想要将多比特率资产输出用于结合 Azure 媒体服务提供的动态打包功能时，需要对每个不同的比特率与分辨率生成多个 GOP 对齐的 MP4 文件。 为此，可以先完成[将 MXF 编码为单比特率 MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) 演练。

![启动工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

启动工作流

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>添加一个或多个其他 MP4 输出
我们生成的 Azure 媒体服务资产中的每个 MP4 文件，会支持不同的比特率与分辨率。 让我们将一个或多个 MP4 输出文件添加到工作流。

为了确保使用相同的设置来创建视频编码器，最方便的方式是复制现有的 AVC 视频编码器，并设置其他分辨率与比特率的组合（让我们添加 960x540，每秒 25 帧，2.5 Mbps 的组合）。 若要复制现有的编码器，请在设计器图面中执行复制粘贴。

将“媒体文件输入”的“未压缩的视频”输出插针连接到我们的新 AVC 组件。

![已连接第二个 AVC 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

已连接第二个 AVC 编码器

现在编辑我们的新 AVC 编码器配置，以 2.5 Mbps 输出 960x540。 （对此使用其属性“输出宽度”、“输出高度”和“比特率(kbps)”。）

假设我们想要使用生成的资产配合 Azure 媒体服务的动态打包，流处理终结点必须能够从这些 MP4 文件生成 HLS/分段的 MP4/DASH 片段，这些片段以要在不同的比特率之间切换来获得单一顺畅的连续视频和音频体验的客户端的方式彼此完全对应。 为了实现此目的，我们需要确保两个 MP4 文件的 AVC 编码器与 GOP（“图片组”）大小的属性设置为 2 秒，可以通过以下方式完成：

* 将 GOP 大小模式设置为固定 GOP 大小，并
* 将关键帧间隔设置为两秒。
* 同时将“GOP IDR 控制”设置为“关闭 GOP”以确保所有 GOP 独立而没有依赖性

为了方便了解我们的工作流，请将第一个 AVC 编码器重命名为“AVC 视频编码器 640x360 1200kbps”，将第二个 AVC 编码器重命名为“AVC 视频编码器 960x540 2500 kbps”。

现在添加第二个“ISO MPEG-4 多路复用器”和第二个“文件输出”。 将多路复用器连接到新的 AVC 编码器，并确保其输出定向到“文件输出”。 然后将 AAC 音频编码器输出连接到新的多路复用器输入。 接着可以将“文件输出”连接到“输出文件/资产”节点，以将它添加到要创建的“媒体服务资产”。

![已连接第二个多路复用器和文件输出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

已连接第二个多路复用器和文件输出

为了与 Azure 媒体服务动态打包兼容，请将多路复用器的“区块模式”设置为 GOP 计数或持续时间，并将每个区块的 GOP 设置为 1。 （这应该是默认值。）

![多路复用器区块模式](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

多路复用器区块模式

注意：可以对要添加到资产输出的任何其他比特率和分辨率组合重复此过程。

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>配置文件输出名称
我们已将多个文件添加到输出资产。 这使得需要确定每个输出文件的文件名彼此不同，并甚至可能应用文件命名约定，使得能够从文件名清楚知道要处理的是什么。

文件输出命名可以通过设计器中的表达式来控制。 打开其中一个“文件输出”组件的属性窗格，并打开“文件属性”的表达式编辑器。 第一个输出文件是通过以下表达式配置的（请参阅有关[从 MXF 转为单比特率 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)的教程）：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

这意味着我们的文件名由两个变量决定：要写入的输出目录和源文件基本名称。 前者在工作流根目录上公开为属性，后者则由传入的文件决定。 请注意，输出目录是用于本地测试的目录；当 Azure 媒体服务中基于云的媒体处理器执行工作流时，此属性由工作流引擎重写。
要提供这两个输出文件一致的输出命名，请将第一个文件命名表达式更改为：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

将第二个文件命名表达式更改为：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

执行临时测试回合，以确保正确生成这两个 MP4 输出文件。

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>添加单独的音轨
如我们稍后所见，生成要随着我们的 MP4 输出文件发送的 .ism 文件时，我们也将需要仅限音频的 MP4 文件作为自适应流的音轨。 要创建此文件，请将附加的多路复用器添加到工作流（ISO-MPEG-4 多路复用器），并将 AAC 编码器的输出插针与其音轨 1 输入插针连接。

![已添加音频多路复用器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

已添加音频多路复用器

创建第三个“文件输出”组件，以从多路复用器输出出站流，并将文件命名表达式配置为：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![用于创建输出文件的音频多路复用器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

用于创建输出文件的音频多路复用器

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>添加 .ISM SMIL 文件
为了让动态打包能在我们的媒体服务资产中结合这两个 MP4 文件（仅限音频的 MP4）运行，我们还需要一个清单文件（也称为“SMIL”文件：同步多媒体集成语言）。 此文件可向 Azure 媒体服务指出哪些 MP4 文件可供动态打包，以及要考虑进行音频流式处理的文件。 包含单个音频流的一组 MP4 的典型清单文件类似于下面：

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

.ism 文件中包含 switch 语句、对每个 MP4 视频文件的引用，此外还有对只包含音频的 MP4 的一个（或多个）音频文件的引用。

为我们的一组 MP4 生成清单文件，可通过名为“AMS 清单编写器”的组件完成。 要使用该组件，请将它拖放到图面上并将“编写完成”输出插针从三个“文件输出”组件连接到“AMS 清单编写器”输入。 然后确保将“AMS 清单编写器”的输出连接到“输出文件/资产”。

如同对我们的其他文件输出组件，使用表达式来配置 .ism 文件输出名称：

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

完成的工作流如下所示：

![已完成 MXF 到多比特率 MP4 的工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

已完成 MXF 到多比特率 MP4 的工作流

## <a id="MXF_to__multibitrate_MP4"></a>将 MXF 编码为多比特率 MP4 - 增强的蓝图
在[前一个工作流演练](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)中，我们已了解单个 MXF 输入资产如何转换成输出资产，其具有多比特率 MP4 文件、仅限音频的 MP4 文件和用于与 Azure 媒体服务动态打包结合使用的清单文件。

本演练会演示如何增强一些方面并提高其方便性。

### <a id="MXF_to_multibitrate_MP4_overview"></a>要增强的工作流概述
![要增强的多比特率 MP4 工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

要增强的多比特率 MP4 工作流

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>文件命名约定
在前面的工作流中，我们已将一个简单的表达式指定为生成输出文件名的基础。 不过，我们有一些重复项目：所有单个输出文件组件都指定了此类表达式。

例如，我们的第一个视频文件的文件输出组件使用了此表达式配置：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

对于第二个输出视频，我们使用了如下所示的表达式：

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

如果我们可以删除某些重复项目，并改为使得项目更易于配置，是不是更清楚、更不容易出错且更方便？ 幸好我们可以做到这一点：设计器的表达式功能结合能够在我们的工作流根目录上创建自定义属性，让我们多了一层便利性。

假设我们将从单个 MP4 文件的比特率驱动文件名的配置。 我们将力求在一个中心位置（在图形的根目录）配置的这些比特率，将从该位置访问它们，以配置和驱动文件名生成。 为此，我们从来自两个 AVC 编码器的比特率属性发布到我们的工作流根目录开始，使其成为可从根目录及从 AVC 编码器访问。 （即使显示在两个不同的位置，也只有一个基本值。）

### <a id="MXF_to__multibitrate_MP4_publishing"></a>将组件属性发布到工作流根目录
打开第一个 AVC 编码器，转到“比特率(kbps)”属性，并从下拉列表中选择“发布”。

![发布比特率属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

发布比特率属性

配置发布对话框，以发布到工作流图形的根目录，使用发布的名称“video1bitrate”以及可读的显示名称“视频 1 比特率”。 配置名为“流比特率”的自定义组名称，并点击“发布”。

![发布比特率属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

比特率属性发布对话框

对第二个 AVC 编码器的比特率属性重复相同的操作，并在相同的自定义组“流比特率”中将它命名为“video2bitrate”，以及显示名称“视频 2 比特率”。

如果我们现在检查工作流根目录属性，就会看到自定义组显示了这两个发布的属性。 两者都反映了其各自的 AVC 编码器比特率的值。

![已发布工作流根目录中的比特率属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

每当我们想要从代码或通过表达式访问这些属性时，就可以这样做：

* 根目录下的组件中的内联代码：node.getPropertyAsString('../video1bitrate',null)
* 在表达式中：${ROOT_video1bitrate}

让我们通过也在其上发布我们的音轨比特率来完成“流比特率”组。 在 AAC 编码器的属性内，搜索“比特率”设置，并从它旁边的下拉列表中选择“发布”。 发布到在自定义组“流处理比特率”内名为“audio1bitrate”和显示名称为“音频 1 比特率”的图形根目录中。

![音频比特率发布对话框](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

音频比特率发布对话框

![根目录中生成的视频和音频属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

根目录中生成的视频和音频属性

请注意，对这三个值的任何更改还会重新配置并更改所链接（和发布源位置）的单个组件的值。

### <a id="MXF_to__multibitrate_MP4_output_files"></a>使生成的输出文件名依赖于发布的属性值
不要对我们生成的文件名进行硬编码，我们现在可以在每个“文件输出”组件上更改文件名，以依赖刚刚在图形根目录上发布的表达式属性。 从第一个文件输出开始，查找文件属性，并编辑表达式，如下所示：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

可以通过在表达式窗口中点击键盘上的货币符号来访问及输入此表达式中的不同参数。 其中一个可用的参数是前面发布的 video1bitrate 属性。

![访问表达式中的参数](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

访问表达式中的参数

对第二个视频的文件输出执行相同的操作：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

针对仅含音频文件的输出：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

如果我们现在更改任何视频或音频文件的比特率，将重新配置单个编码器，并对所有项目自动使用基于比特率的文件名约定。

## <a id="thumbnails_to__multibitrate_MP4"></a>将缩略图添加到多比特率 MP4 输出
从[通过 MXF 输入生成多比特率 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)的工作流开始，我们现在要将缩略图添加到输出。

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>要将缩略图添加到的工作流概述
![要从中开始的多比特率 MP4 工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

要从中开始的多比特率 MP4 工作流

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>添加 JPG 编码
缩略图生成核心是可以输出 JPG 文件的 JPG 编码器组件。

![JPG 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

JPG 编码器

不过我们无法直接将未压缩的视频流从媒体文件输入连接到 JPG 编码器。 相反地，它预期收到单个帧。 可以通过“视频帧门限”组件执行此操作。

![将帧门限连接到 JPG 编码器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

将帧门限连接到 JPG 编码器

帧门限每隔许多秒或帧执行一次，可让视频帧传递。 发生的间隔和时间偏移可在属性中配置。

![视频帧门限属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

视频帧门限属性

让我们通过将模式设置为时间（秒）及间隔设置为 60，每隔一分钟创建缩略图。

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>处理颜色空间转换
尽管逻辑上可看到帧门限和媒体文件输入的未压缩的视频插针现在已可连接，如果我们想执行此操作，则收到警告。

![输入颜色空间错误](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

输入颜色空间错误

这是因为在我们原始原始未压缩的视频流（来自 MXF）中，颜色信息的表示方式与 JPG 编码器所预期的不同。 更具体地说，预期流入称为“RGB”或“灰阶”的“颜色空间”。 这意味着，视频帧门限的输入视频流，需要先应用有关其颜色空间的转换。

拖放到工作流上的“颜色空间转换器 - Intel”，并将它连接到帧门限。

![连接颜色空间转换器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

连接颜色空间转换器

在属性窗口中，从“预设”列表中选择 BGR 24 项。

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>写入缩略图
不同于 MP4 视频，JPG 编码器组件将输出多个文件。 为了解决此问题，可以使用“场景搜索 JPG 文件编写器”组件：它采用传入的 JPG 缩略图并写出，每个文件名末尾加上不同的数字。 （数字通常表示缩略图取自流中的秒数/单位数。）

![引入场景搜索 JPG 文件写入器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

引入场景搜索 JPG 文件编写器

使用以下表达式配置输出文件夹路径属性：${ROOT_outputWriteDirectory}

配置文件名前缀属性：

    ${ROOT_sourceFileBaseName}_thumb_

前缀确定缩略图文件命名的方式。 它们的前面带有数字前缀，指示缩略图在流中的位置。

![场景搜索 JPG 文件编写器属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

场景搜索 JPG 文件编写器属性

将“场景搜索 JPG 文件编写器”连接到“输出文件/资产”节点。

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>在工作流中检测错误
将颜色空间转换器的输入连接到原始未压缩的视频输出。 现在，对工作流执行本地测试回合。 工作流很可能突然停止执行，并在发生错误之组件上以红色外框指示：

![颜色空间转换器错误](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

颜色空间转换器错误

单击颜色空间转换器组件右上角的小型红色“E”图标，以查看编码尝试失败的原因。

![颜色空间转换器错误对话框](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

颜色空间转换器错误对话框

在结果中可以看到，对于我们要求 YUV 到 RGB 的转换，颜色空间转换器的传入颜色空间标准必须为 rec601。 显然流并未指出它是 rec601。 （Rec 601 是以数字视频格式编码交错式模拟视频信号的标准。 它指定涵盖 720 亮度采样和每行 360 色度采样的活动区域。 颜色编码系统称为 YCbCr 4:2:2。）

为了解决此问题，我们在流的元数据上指出我们要处理 rec601 内容。 为此，我们将使用“视频数据类型更新器”组件，将它放在原始源和颜色空间转换组件之间。 使用此数据类型更新器可手动更新特定视频数据类型属性。 对它进行配置，以指定“Rec 601”颜色空间标准。 在尚未定义颜色空间的情况下，这会导致视频数据类型更新器以“Rec 601”颜色空间标记流。 （它不重写任何现有的元数据，除非已选中“重写”复选框。）

![在数据类型更新器中更新颜色空间标准](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

在数据类型更新器中更新颜色空间标准

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>已完成的工作流
现在，我们已完成工作流，接着执行另一个测试回合来查看它的传递。

![包含缩略图的多个 mp4 输出的已完成工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

包含缩略图的多个 mp4 输出的已完成工作流

## <a id="time_based_trim"></a>对多比特率 MP4 输出进行基于时间的修剪
从[通过 MXF 输入生成多比特率 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)的工作流开始，我们现在将基于时间戳修剪源视频。

### <a id="time_based_trim_start"></a>要在其中开始添加修剪的工作流概述
![要在其中添加修剪的起始工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

要在其中添加修剪的起始工作流

### <a id="time_based_trim_use_stream_trimmer"></a>使用流修剪器
流修剪器组件允许根据计时信息（秒、分等等）修剪输入流的开头和末尾。修剪器不支持基于帧的修剪。

![流修剪器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

流修剪器

不要直接将 AVC 编码器和扬声器位置分配器链接到媒体文件输入，而是将它们放在流修剪器之间。 （一个用于视频信号，一个用于交错的音频信号。）

![在中间放入流修剪器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

在中间放入流修剪器

让我们配置修剪器，以便只处理视频中 15 秒到 60 秒的视频和音频。

转到视频流修剪器属性，并配置开始时间（15 秒）和结束时间（60 秒）属性。 要确保音频和视频修剪器始终同时配置为相同的开始与结束值，我们将它们发布到工作流根目录。

![从流修剪器发布开始时间属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

从流修剪器发布开始时间属性

![开始时间发布属性对话框](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

开始时间发布属性对话框

![结束时间发布属性对话框](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

结束时间发布属性对话框

如果我们现在检查工作流根目录，这两个属性将整齐地显示，且可从该处配置。

![已发布根目录中提供的属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

已发布根目录中提供的属性

现在从音频修剪器打开修剪属性，并使用引用工作流根目录上所发布属性的表达式来配置开始和结束时间。

针对音频修剪开始时间：

    ${ROOT_TrimmingStartTime}

针对结束时间：

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>已完成的工作流
![已完成的工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*已完成的工作流*

## <a id="scripting"></a>引入脚本组件
脚本组件可以在工作流执行阶段执行任意脚本。 有四个可以执行的不同脚本，每个都具有特定特征，以及在工作流生命周期中的位置：

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

脚本组件的文档更详细说明了上述各个脚本。 在[下一部分](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)，**realizeScript** 脚本组件用于在工作流启动时快速构造剪辑列表 xml。 在组件安装期间调用此脚本，这种情况在其生命周期中只发生一次。

### <a id="scripting_hello_world"></a>在工作流中编写脚本：hello world
将脚本组件拖放到设计器图面上，并重命名（例如，“SetClipListXML”）。

![添加脚本组件](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

添加脚本组件

检查脚本组件的属性时，会显示四种不同的脚本类型，而每种类型可配置到不同的脚本。

![脚本组件属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

脚本组件属性

清除 processInputScript，并打开 realizeScript 的编辑器。 现在我们已完成设置并准备开始编写脚本。

脚本以 Groovy 编写，它是适用于 Java 平台的动态编译脚本语言，能与 Java 保持兼容。 事实上，大多数 Java 代码都是有效的 Groovy 代码。

让我们在 realizeScript 的上下文中编写一个简单的 Hello World Groovy 脚本。 在编辑器中输入以下命令：

    node.log("hello world");

现在执行本地测试回合。 完成此回合后，（通过“脚本组件”上的“系统”选项卡）检查“日志”属性。

![Hello world 日志输出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

Hello world 日志输出

调用日志方法所在的节点对象是指当前的“节点”或者我们正在编写脚本的组件。 每个组件因此具备可通过系统选项卡输出日志记录数据的能力。这里，我们输出字符串文本“Hello World”。 在此处需要了解的是，这确实是非常重要的调试工具，可深入了解脚本实际上做些什么。

从脚本环境内，我们也可以访问其他组件的属性。 试试看：

    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

日志窗口显示：

![有关访问节点路径的日志输出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

有关访问节点路径的日志输出

## <a id="frame_based_trim"></a>对多比特率 MP4 输出进行基于帧的修剪
从[通过 MXF 输入生成多比特率 MP4 输出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)的工作流开始，我们现在将基于帧计数修剪源视频。

### <a id="frame_based_trim_start"></a>要在其中开始添加修剪的蓝图概述
![要在其中开始添加修剪的工作流](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

要在其中开始添加修剪的工作流

### <a id="frame_based_trim_clip_list"></a>使用剪辑列表 XML
在所有以前的工作流教程中，我们使用“媒体文件输入”组件作为视频输入源。 不过，在此特定方案中，我们将改为使用剪辑列表源组件。 请注意，这应该不是最好的工作方式；只在有实际原因这么做时才使用剪辑列表源（如同在以下情况下，我们使用剪辑列表修剪功能）。

要从“媒体文件输入”切换到“剪辑列表源”，请将“剪辑列表源”组件拖放到设计图面，并将“剪辑列表 XML”插针连接到工作流设计器的“剪辑列表 XML”插针。 这应会根据输入视频，以输出插针填充剪辑列表源。 现在，从剪辑列表源将“未压缩的视频”和“未压缩的音频”插针连接到相应的“AVC 编码器”和“音频流交错器”。 现在删除媒体文件输入。

![已将媒体文件输入替换为剪辑列表源](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

已将媒体文件输入替换为剪辑列表源

剪辑列表源组件将它视为输入“剪辑列表 XML”。 选择要在本地测试的源文件，自动填充此剪辑列表 XML。

![自动填充的剪辑列表 XML 属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

自动填充的剪辑列表 XML 属性

仔细观察 XML，这是其外观：

![编辑剪辑列表对话框](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

编辑剪辑列表对话框

不过这未反映剪辑列表 XML 的功能。 我们拥有的其中一个选项是在视频和音频源下添加“修剪”元素，如下所示：

![将修剪元素添加到剪辑列表](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

将修剪元素添加到剪辑列表

如果修改类似于上面的剪辑列表 XML 并执行本地测试回合，你会看到视频已正确在视频中修剪为 10 到 20 秒。

不过，相对于当执行本地执行时发生的情况，在 Azure 媒体服务中执行的工作流中，此完全相同的剪辑列表 XML 不会有相同的效果。 Azure 高级编码器启动时，每次都会根据提供给编码作业的输入文件生成剪辑列表 XML。 这意味着，我们在 XML 上执行的任何更改不幸地被重写。

若要避免剪辑列表 XML 在编码作业开始时被擦除，我们可以在工作流启动之后快速重新生成它。 通过称为“脚本组件”的组件即可以采取这种自定义操作。 有关详细信息，请参阅[引入脚本组件](media-services-media-encoder-premium-workflow-tutorials.md#scripting)。

将脚本组件拖放到设计器图面上，并重命名为“SetClipListXML”。

![添加脚本组件](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

添加脚本组件

检查脚本组件的属性时，会显示四种不同的脚本类型，而每种类型可配置到不同的脚本。

![脚本组件属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

脚本组件属性

### <a id="frame_based_trim_modify_clip_list"></a>通过脚本组件修改剪辑列表
在我们可以重新写入工作流启动时生成的剪辑列表 XML 之前，需要访问剪辑列表 XML 属性和内容。 可按如下所示执行操作：

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![正在记录传入的剪辑列表](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

正在记录传入的剪辑列表

首先，我们需要确定想要修剪视频的哪一个点到哪一个点。 为了让它方便工作流较不具技术性的用户，请将两个属性发布到图形的根目录。 为此，请右键单击设计器图面并选择“添加属性”：

* 第一个属性：类型为“TIMECODE”的“ClippingTimeStart”
* 第二个属性：类型为“TIMECODE”的“ClippingTimeEnd”

![添加属性对话框中的剪辑开始时间](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

添加属性对话框中的剪辑开始时间

![已发布工作流根目录中的剪辑时间属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

已发布工作流根目录中的剪辑时间属性

将这两个属性配置为适当的值：

![配置剪辑开始和结束属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

配置剪辑开始和结束属性

现在，从脚本内，我们可以访问这两个属性，如下所示：

    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![显示剪辑开始与结束时间的日志窗口](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

显示剪辑开始与结束时间的日志窗口

让我们使用简单的正则表达式，将时间代码字符串分析为更方便使用的格式：

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);

![包含已分析时间代码输出的日志窗口](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

包含已分析时间代码输出的日志窗口

准备好此信息后，接下来可以修改剪辑列表 XML 以反映所需的电影帧精确剪辑开始和结束时间。

![用于添加修剪元素的脚本](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

用于添加修剪元素的脚本代码

这可以通过一般的字符串处理操作完成。 生成的经修改剪辑列表 XML 通过“setProperty”方法写回工作流根目录上的 clipListXML 属性。 在另一个测试回合之后日志窗口显示以下内容：

![记录生成的剪辑列表](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

记录生成的剪辑列表

执行测试回合以查看视频和音频流剪辑的情况。 但是，随着你使用不同的值针对修剪点执行多个测试回合，会发现并未考虑到这些因素！ 这是因为设计器不同于 Azure 运行时，不会在每次执行时重写剪辑列表 XML。 这意味着，只有在第一次设置输入和输出点时导致 xml 转换，其他任何时候临界子句 (if(clipListXML.indexOf("<trim>") == -1)) 都会在已经存在一个元素时防止工作流添加另一个修剪元素。

为了让工作流方便在本地测试，我们最好添加一些监护代码，用于检查是否已经存在修剪元素。 如果是的话，我们可以在继续之前，将 XML 修改为新的值来将它删除。 不要使用纯文本字符串操作，通过实际的 XML 对象模型分析执行此操作可能更安全。

在可以添加此类代码之前，我们还需要先在脚本的开头添加一些导入语句：

    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

在此之后，可以添加所需的清除代码：

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

此代码放在添加修剪元素到剪辑列表 XML 的点的正上方。

此时，只要需要，我们可以在让更改随着时间应用时执行及修改工作流。    

### <a id="frame_based_trim_clippingenabled_prop"></a>添加 ClippingEnabled 便利属性
因为可能不要始终进行修剪，让通过添加方便的布尔值标志（可指出是否要启用修剪/剪辑）来完成工作流。

如前面一样，将一个新属性发布到类型为“BOOLEAN”、名为“ClippingEnabled”的工作流的根目录。

![已发布用于启用剪辑的属性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

已发布用于启用剪辑的属性

使用以下简单的成立条件子句可以检查是否需要修剪，并确定是否因此需要修改剪辑列表。

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }


### <a id="code"></a>完整代码
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }


## <a name="also-see"></a>另请参阅
[在 Azure 媒体服务中引入高级编码](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[如何在 Azure 媒体服务中使用高级编码](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[使用 Azure 媒体服务对请求内容进行编码](media-services-encode-asset.md#media-encoder-premium-workflow)

[媒体编码器高级工作流格式和编解码器](media-services-premium-workflow-encoder-formats.md)

[示例工作流文件](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure 媒体服务资源管理器工具](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
