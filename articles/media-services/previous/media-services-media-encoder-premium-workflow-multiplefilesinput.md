---
title: 高级编码器中的多个输入文件和组件属性 - Azure | Microsoft 文档
description: 本主题说明如何使用 setRuntimeProperties 来使用多个输入文件并将自定义数据传递到媒体编码器高级工作流媒体处理器。
services: media-services
documentationcenter: ''
author: xpouyat
manager: cfowler
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 66aec76e5af399e1909446b8ddf7a79aa1384d52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783756"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>在高级编码器中使用多个输入文件和组件属性
## <a name="overview"></a>概述
在某些情况下，可能需要在使用**媒体编码器高级工作流**媒体处理器提交任务时自定义组件属性、指定剪辑列表 XML 内容或发送多个输入文件。 下面是一些示例：

* 在每个输入视频运行时，覆盖视频中的文本和设置文本值（例如，当前日期）。
* 自定义剪辑列表 XML（以指定一个或多个包含或不包含剪裁的源文件等）。
* 编码视频时覆盖输入视频中的徽标图像。
* 多个音频语言编码。

若要在创建任务或发送多个输入文件时让**媒体编码器高级工作流**知道要更改工作流中的某些属性，必须使用包含 **setRuntimeProperties** 和/或 **transcodeSource** 的配置字符串。 本主题说明其使用方式。

## <a name="configuration-string-syntax"></a>配置字符串语法
要在编码任务中设置的配置字符串使用类似以下的 XML 文档：

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

以下 C# 代码会从文件中读取 XML 配置、将其更新为正确的视频文件名并将其传递到作业中的任务：

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>自定义组件属性
### <a name="property-with-a-simple-value"></a>具有简单值的属性
在某些情况下，适合在媒体编码器高级工作流将要执行的工作流文件中一起自定义组件属性。

假设设计了一个工作流，用于覆盖视频中文本，而这些文本（例如，当前日期）应在运行时进行设置。 为此，可以从编码任务发送设置为覆盖层组件文本属性的新值的文本。 可使用此机制更改工作流中的组件的其他属性（例如覆盖层的位置或颜色、AVC 编码器的比特率等）。

**setRuntimeProperties** 用于覆盖工作流组件中的属性。

示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>具有 XML 值的属性
若要设置预期具有 XML 值的属性，可使用 `<![CDATA[ and ]]>` 进行封装。

示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> 请确保不要在 `<![CDATA[` 后面插入回车。

### <a name="propertypath-value"></a>propertyPath 值
在前面的示例中，propertyPath 为“/Media File Input/filename”、“/inactiveTimeout”或“clipListXml”。
通常是组件名称后接属性名称。 路径可以包含更多或更少的级别，例如“/primarySourceFile”（因为属性位于工作流的根目录）或“/Video Processing/Graphic Overlay/Opacity”（因为覆盖层在组中）。    

若要检查路径和属性名称，使用紧靠在每个属性旁边的操作按钮。 可以单击此操作按钮，并选择“编辑”。 随后会显示属性的实际名称，并在其正上方显示命名空间。

![操作/编辑](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![属性](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>多个输入文件
提交给**媒体编码器高级工作流**的每个任务需要两个资产：

* 第一个是工作流资产，其中包含工作流文件。 可以使用[工作流设计器](media-services-workflow-designer.md)来设计工作流文件。
* 第二个是媒体资产，其中包含要编码的媒体文件。

将多个媒体文件发送到**媒体编码器高级工作流**编码器时，存在以下限制：

* 所有媒体文件必须位于同一个媒体资产中。 不支持使用多个媒体资产。
* 必须在此媒体资产中设置主文件（理想情况下，这是请求编码器处理的主要视频文件）。
* 必须将包含 **setRuntimeProperties** 和/或 **transcodeSource** 元素的配置数据传递给处理器。
  * **setRuntimeProperties** 用于覆盖工作流组件中的文件名属性或其他属性。
  * **transcodeSource** 用于指定剪辑列表 XML 内容。

工作流中的连接：

* 如果使用一个或多个媒体文件输入组件，并打算使用 **setRuntimeProperties** 来指定文件名，请勿将主文件组件插针连接到这些组件。 确保主文件对象与媒体文件输入之间没有连接。
* 如果偏好使用剪辑列表 XML 和一个媒体源组件，则可以将两者连接在一起。

![主源文件与媒体文件输入之间未建立连接](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

如果使用 setRuntimeProperties 来设置文件名属性，主文件不会连接到媒体文件输入组件。

![从剪辑列表 XML 到剪辑列表源的连接](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

可以将剪辑列表 XML 连接到媒体源并使用 transcodeSource。

### <a name="clip-list-xml-customization"></a>剪辑列表 XML 自定义
可以在配置字符串 XML 中使用 **transcodeSource**，以在运行时于工作流中指定剪辑列表 XML。 这需要剪辑列表 XML 插针才能连接到工作流中的媒体源组件。

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

如果要通过使用“Expressions”来指定 /primarySourceFile，以使用此属性来命名输出文件，则建议在 /primarySourceFile 属性的后面将剪辑列表 XML 作为属性传递，以避免剪辑列表被 /primarySourceFile 设置覆盖。

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

使用其他帧精确修整：

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>示例 1：覆盖视频顶层的图像

### <a name="presentation"></a>呈现
假设要在视频编码时覆盖输入视频中的徽标图像。 此示例中，输入视频命名为“Microsoft_HoloLens_Possibilities_816p24.mp4”，徽标命名为“logo.png”。 应该执行以下步骤：

* 创建包含工作流文件的工作流资产（参阅以下示例）。
* 创建包含以下两个文件的媒体资产：MyInputVideo.mp4（主文件）和 MyLogo.png。
* 使用上述输入资产将任务发送到媒体编码器高级工作流媒体处理器，并指定以下配置字符串。

配置:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

在以上示例中，视频文件的名称已发送到媒体文件输入组件和 primarySourceFile 属性。 徽标文件的名称已发送到另一个连接到图形覆盖层组件的媒体文件输入。

> [!NOTE]
> 视频文件名已发送到 primarySourceFile 属性。 这样做的目的是要在工作流中使用此属性，以便（举例而言）使用 Expressions 构建正确的输出文件名。

### <a name="step-by-step-workflow-creation"></a>分步工作流创建
以下步骤将创建一个工作流，该工作流采用视频和图像两个文件作为输入。 它将覆盖位于视频顶层的图像。

打开“工作流设计器”，并选择“文件” > “新建工作区” > “转码蓝图”。

新工作流显示了三个元素：

* 主源文件
* 剪辑列表 XML
* 输出文件/资产  

![新编码工作流](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

新编码工作流

为了接受输入媒体文件，请从添加媒体文件输入组件开始。 要将组件添加到工作流，请在“存储库”搜索框中查找它，然后将所需的项拖放到设计器窗格。

接下来，添加要用于设计工作流的视频文件。 为此，请单击工作流设计器中的背景窗格，并在右侧属性窗格中找到“主源文件”属性。 单击文件夹图标，并选择相应的视频文件。

![主文件源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

主文件源

接下来，在媒体文件输入组件中指定视频文件。   

![媒体文件输入源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

媒体文件输入源

完成此操作后，媒体文件输入组件会检查文件，并填充其输出插针，以反映其检查的文件。

下一步是添加一个“视频数据类型更新器”，以将颜色空间指定为 Rec.709。 添加设置为“数据布局/布局类型 = 可配置平面”的“视频格式转换器”。 这会将视频流转换为可以用作覆盖层组件源的格式。

![视频数据类型更新器和格式转换器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

视频数据类型更新器和格式转换器

![布局类型 = 可配置平面](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

布局类型是可配置平面

接下来，添加一个视频覆盖层组件，并将（未压缩的）视频插针连接到媒体文件输入的（未压缩的）视频插针。

添加另一个媒体文件输入（用于加载徽标文件），单击此组件并将其重命名为“媒体文件输入徽标”，并在文件属性中选择图像（例如 .png 文件）。 将未压缩的图像插针连接到覆盖层的未压缩图像插针。

![覆盖层组件和图像文件源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

覆盖层组件和图像文件源

若要修改视频中徽标的位置（例如，需要将其放在距离视频左上角 10% 处），可清除“手动输入”复选框。 由于使用媒体文件输入为覆盖层组件提供徽标文件，因此可以执行此操作。

![覆盖层位置](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

覆盖层位置

要将视频流编码成 H.264，请将 AVC 视频编码器和 AAC 编码器组件添加到设计器图面。 连接插针。
设置 AAC 编码器，并选择“音频格式转换/预设：2.0 (L, R)”。

![音频和视频编码器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

音频和视频编码器

现在，添加“ISO Mpeg-4 多路复用器”和“文件输出”组件并连接插针，如下所示。

![MP4 多路复用器和文件输出](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

MP4 多路复用器和文件输出

需要设置输出文件的名称。 单击“文件输出”组件，并编辑文件的表达式：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![文件输出名称](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

文件输出名称

可以在本地运行工作流，以检查它是否正常运行。

完成后，可以在 Azure 媒体服务中运行它。

首先，在 Azure 媒体服务中准备包含以下两个文件的资产：视频文件和徽标。 可以使用 .NET 或 REST API 来执行此操作。 也可以使用 Azure 门户或 [Azure 媒体服务资源管理器](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 来执行此操作。

本教程说明如何使用 AMSE 来管理资产。 有两种方式可将文件添加到资产：

* 创建一个本地文件夹，在其中复制这两个文件，然后将该文件夹拖放到“资产”选项卡。
* 将视频文件作为资产上传，显示资产信息，转到文件选项卡，然后上传另一个文件（徽标）。

> [!NOTE]
> 请务必在资产中设置主文件（主要视频文件）。

![AMSE 中的资产文件](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

AMSE 中的资产文件

选择资产，并选择使用高级编码器将其编码。 上传工作流并选择它。

单击按钮将数据传递到处理器，然后添加以下 XML 来设置运行时属性：

![AMSE 中的高级编码器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

AMSE 中的高级编码器

然后，粘贴以下 XML 数据。 需要指定媒体文件输入和 primarySourceFile 的视频文件名。 此外，应指定徽标的文件名。

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

如果使用 .NET SDK 来创建和运行任务，则必须将此 XML 数据作为配置字符串传递。

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

作业完成后，输出资产中的 MP4 文件会显示覆盖层！

![视频中的覆盖层](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

视频中的覆盖层

可以从 [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/) 下载示例工作流。

## <a name="example-2--multiple-audio-language-encoding"></a>示例 2：多个音频语言编码

可在 [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding) 中获取多个音频语言编码工作流的示例。

此文件夹中包含的示例工作流可用于将 MXF 文件编码为具有多个音频轨道的多个 MP4 文件资产。

此工作流假定该 MXF 文件包含一个音频轨道；其他音频轨道应作为单独的音频文件（WAV 或 MP4...）进行传递。

若要编码，请执行以下步骤：

* 创建包含 MXF 文件和音频文件（0 - 18 个音频文件）的媒体服务资产。
* 确保将 MXF 文件设置为主文件。
* 使用 Premium Workflow Encoder 处理器创建作业和任务。 使用所提供的工作流 (MultiMP4-1080p-19audio-v1.workflow)。
* 将 setruntime.xml 数据传递到任务（如果使用 Azure 媒体服务资源管理器，请使用“将 xml 数据传递到工作流”按钮）。
  * 请更新 XML 数据，指定正确的文件名和语言标记。
  * 此工作流包含命名为 Audio 1 到 Audio 18 的音频组件。
  * 语言标记支持 RFC5646。

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* 编码资产将包含多个语言音频轨道，且可在 Azure Media Player 中选择这些轨道。

## <a name="see-also"></a>另请参阅
* [在 Azure 媒体服务中引入高级编码](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [如何在 Azure 媒体服务中使用高级编码](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [使用 Azure 媒体服务对按需内容进行编码](media-services-encode-asset.md#media-encoder-premium-workflow)
* [媒体编码器高级工作流格式和编解码器](media-services-premium-workflow-encoder-formats.md)
* [示例工作流文件](https://github.com/Azure/azure-media-services-samples)
* [Azure 媒体服务资源管理器工具](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
