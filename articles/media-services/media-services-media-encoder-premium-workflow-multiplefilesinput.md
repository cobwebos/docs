<properties 
	pageTitle="在高级编码器中使用多个输入文件和组件属性 | Microsoft Azure" 
	description="本主题说明如何通过 setRuntimeProperties 来使用多个输入文件，并将自定义数据传递到媒体编码器高级工作流媒体处理器。" 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.date="07/12/2016"  
	wacn.date=""/>

#在高级编码器中使用多个输入文件和组件属性

##概述 

在某些情况下，你可能需要在使用**媒体编码器高级工作流**媒体处理器提交任务时自定义组件属性、指定剪辑列表 XML 内容，或发送多个输入文件。下面是一些示例：

- 在运行每个输入视频时，覆盖视频中的文本和设置文本值（例如，当前日期）
- 自定义剪辑列表 XML（以指定一个或多个包含或不包含修剪的源文件，等等）
- 编码时覆盖输入视频中的徽标图像

若要在创建任务或发送多个输入文件时让**媒体编码器高级工作流**知道要更改工作流中的某些属性，必须使用包含 **setRuntimeProperties** 和/或 **transcodeSource** 的配置字符串。本主题说明如何使用这些属性。


##配置字符串语法

要在编码任务中设置的配置字符串使用类似于下面的 XML 文档：
  
    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


C# 中用于从文件读取 XML 配置并将它传递给作业中的任务的代码是：

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


##自定义组件属性  

###具有简单值的属性
在某些情况下，适合在媒体编码器高级工作流将要执行的工作流文件中一起自定义组件属性。假设你设计了一个工作流，用于覆盖视频中文本，而这些文本（例如，当前日期）应在运行时进行设置。为此，可以从编码任务发送文本，作为覆盖层组件文本属性的新值。可以使用此机制来更改工作流组件的其他属性（例如更改覆盖层的位置或颜色、更改 AVC 编码器的比特率，等等）。**setRuntimeProperties** 用于覆盖工作流组件中的属性。

示例：

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


###具有 XML 值的属性

若要设置一个预期使用 XML 值的属性，请使用 <![CDATA[ 和 ]]> 进行封装

示例：

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

>[AZURE.NOTE]请确保不要在 <![CDATA[ 后面插入回车符


###propertyPath 值

在前面的示例中，propertyPath 为“/Media File Input/filename”、“/inactiveTimeout”或“clipListXml”。这通常是组件名称后接属性名称。路径中可以包含更多或更少的级别，例如“/primarySourceFile”（因为属性是工作流的根目录），或“/Video Processing/Graphic Overlay/Opacity”（因为覆盖层在组中）。

若要检查路径和属性名称，请使用紧靠在每个属性旁边的操作按钮。可以单击此操作按钮，然后选择“编辑”。随后将显示属性的实际名称，并在其正上方显示命名空间。

![操作/编辑](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![属性](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

##多个输入文件

提交给**媒体编码器高级工作流**的每个任务需要两个资产：

- 第一个是“工作流资产”，其中包含工作流文件。可以使用[工作流设计器](/documentation/articles/media-services-workflow-designer/)来设计工作流文件。
- 第二个是“媒体资产”，其中包含你要编码的媒体文件。

将多个媒体文件发送到**媒体编码器高级工作流**编码器时存在以下限制：

- 所有媒体文件必须位于同一个“媒体资产”中。不支持使用多个媒体资产。
- 必须在此媒体资产中设置主文件（理想情况下，这是请求编码器处理的主要视频文件）。
- 必须将包含 **setRuntimeProperties** 和/或 **transcodeSource** 元素的配置数据传递给处理器。
  - **setRuntimeProperties** 用于覆盖工作流组件中的文件名或其他属性
  - **transcodeSource** 用于指定剪辑列表 XML 内容

工作流中的连接
  - 如果你使用一个或多个媒体文件输入组件，并打算使用 **setRuntimeProperties** 来指定文件名，请勿将主文件组件 PIN 连接到这些组件。确保主文件对象与媒体文件输入之间没有连接。
  - 如果你偏好使用剪辑列表 XML 和一个媒体源组件，则可以将两者连接在一起。

![主源文件与媒体文件输入之间未建立连接](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

如果使用 setRuntimeProperties 来设置文件名，主文件不会连接到媒体文件输入组件


![从剪辑列表 XML 到剪辑列表源的连接](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

可以将剪辑列表 XML 连接到媒体源并使用 transcodeSource


###剪辑列表 XML 自定义
可以在配置字符串 XML 中使用 **sourceTranscode**，以便在运行时于工作流中指定剪辑列表 XML。这需要剪辑列表 XML 插针才能连接到工作流中的媒体源组件。

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

如果你想要指定 /primarySourceFile 来使用此属性，以便使用“Expressions”来命名输出文件，则建议在 /primarySourceFile 属性的*后面*将剪辑列表 XML 传递为属性，以避免剪辑列表被 /primarySourceFile 设置覆盖。

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

使用其他帧精确修剪：

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


##示例

假设你要在编码时覆盖输入视频中的徽标图像。在此示例中，输入视频名为“MyInputVideo.mp4”，徽标名为“MyLogo.png”。应执行以下步骤：

- 创建包含工作流文件的工作流资产（参阅以下示例）
- 创建包含以下两个文件的媒体资产：MyInputVideo.mp4（主文件）和 MyLogo.png。
- 使用上述输入资产将任务发送到媒体编码器高级工作流媒体处理器，并指定以下配置字符串

配置:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


在上述示例中，视频文件的名称已发送到媒体文件输入组件和 primarySourceFile 属性，徽标文件的名称已发送到与图形覆盖层组件连接的另一个媒体文件输入。

>[AZURE.NOTE]视频文件名已发送到 primarySourceFile 属性。原因是要在工作流中使用此属性，以便（举例而言）使用表达式构建正确的输出文件名。


###覆盖视频顶层徽标的工作流创建方法逐步说明     

以下步骤将创建一个工作流，该工作流采用两个文件作为输入：视频和图像。它将覆盖位于视频顶层的图像。

打开“工作流设计器”，然后选择“文件”->“新建工作区”->“转码蓝图”

新工作流显示了 3 个元素：

- 主源文件
- 剪辑列表 XML
- 输出文件/资产

![新编码工作流](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

新编码工作流


为了接受输入媒体文件，请从添加媒体文件输入组件开始。若要将组件添加到工作流，请在“存储库”搜索框中查找它，然后将所需的项拖放到设计器窗格。

接下来，添加要用于设计工作流的视频文件。为此，请单击工作流设计器中的背景窗格，并在右侧属性窗格中找到“主源文件”属性。单击文件夹图标，然后选择相应的视频文件。

![主文件源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

主文件源


接下来，在媒体文件输入组件中指定视频文件。

![媒体文件输入源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

媒体文件输入源


完成此操作之后，媒体文件输入组件将检查文件，并填充其输出插针，以反映它检查的文件。

下一步是添加一个“视频数据类型更新器”，以将颜色空间指定为 Rec.709。添加一个“视频格式转换器”并将其“数据布局/布局类型”设置为“可配置平面”。这会将视频流转换为可以用作覆盖层组件源的格式。

![视频数据类型更新器和格式转换器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

视频数据类型更新器和格式转换器

![布局类型 = 可配置平面](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

布局类型 = 可配置平面

接下来，添加一个视频覆盖层组件，并将（未压缩的）视频插针连接到媒体文件输入的（未压缩的）视频插针。

添加另一个媒体文件输入（用于加载徽标文件），单击此组件并将它重命名为“媒体文件输入徽标”，然后在文件属性中选择图像（例如 png 文件）。将未压缩的图像插针连接到覆盖层的未压缩图像插针。

![覆盖层组件和图像文件源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

覆盖层组件和图像文件源


如果想要修改视频中徽标的位置（例如，可以将它放在距离视频左上角 10% 之处），请取消选中“手动输入”。由于你要使用媒体文件输入来向覆盖层组件提供徽标文件，因此可以取消选中此选项。

![覆盖层位置](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

覆盖层位置


若要将视频流编码成 H.264，请将 AVC 视频编码器和 AAC 编码器组件添加到设计器图面。连接插针。设置 AAC 编码器，然后选择：“音频格式转换/预设: 2.0 (L, R)”

![音频和视频编码器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

音频和视频编码器


现在，请添加“ISO Mpeg-4 多路复用器”和“文件输出”组件并连接插针，如下所示。

![MP4 多路复用器和文件输出](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

MP4 多路复用器和文件输出


需要设置输出文件的名称。单击“文件输出”组件，然后编辑文件的表达式：

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![文件输出名称](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

文件输出名称


可以在本地运行工作流，以检查它是否正常运行。

成功运行后，你可以在 Azure 媒体服务中运行它。

首先，在 Azure 媒体服务中准备包含以下两个文件的资产：视频文件和徽标。可以使用 .NET 或 REST API 来执行此操作。为此也可以使用 Azure 门户或 [Azure 媒体服务资源管理器](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE)。

本教程说明如何使用 AMSE 来管理资产。有两种方式可将文件添加到资产。

1. 创建一个本地文件夹，在其中复制这两个文件，然后将该文件夹拖放到“资产”选项卡
1. 上载视频文件作为资产，显示资产信息，转到“文件”选项卡，然后上载另一个文件（徽标）。

>[AZURE.NOTE]请务必在资产中设置主文件（主要视频文件）。

![AMSE 中的资产文件](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

AMSE 中的资产文件


选择资产，并选择使用高级编码器将其编码。上载工作流并选择它。

单击相应的按钮将数据传递到处理器，然后添加以下 XML 来设置运行时属性：

![AMSE 中的高级编码器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

AMSE 中的高级编码器


然后，粘贴以下 XML 数据。需要指定媒体文件输入和 primarySourceFile 的视频文件名。此外，应指定徽标的文件名。

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

setRuntimeProperties


如果使用 .Net SDK 来创建和运行任务，则必须将此 XML 数据传递为配置字符串。

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
  
作业完成后，输出资产中的 MP4 文件将显示覆盖层！

![视频中的覆盖层](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

视频中的覆盖层


可以从[此处](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)下载示例工作流。


##另请参阅 

[在 Azure 媒体服务中引入高级编码](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[如何在 Azure 媒体服务中使用高级编码](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[使用 Azure 媒体服务对按需内容进行编码](/documentation/articles/media-services-encode-asset/#media_encoder_premium_workflow)

[Media Encoder Premium Workflow 格式和编解码器](/documentation/articles/media-services-premium-workflow-encoder-formats/)

[示例工作流文件](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

[Azure 媒体服务资源管理器工具](http://aka.ms/amse)

<!---HONumber=Mooncake_0808_2016-->