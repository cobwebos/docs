<properties linkid="manage-services-mediaservices-manage-media-services-content" urlDisplayName="How to manage media content" pageTitle="如何管理媒体内容 - Azure Media Services" metaKeywords="" description="了解如何管理 Azure Media Services 中的媒体内容。" metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="migree" solutions="" manager="" editor="" />





# 如何管理 Media Services 中的内容 #

本主题说明如何使用 Azure 管理门户来管理 Media Services 帐户中的媒体内容。

当前你可以直接从门户执行以下内容操作：

- 查看内容信息，例如，发布状态、发布的 URL、大小、上次更新日期时间和资产是否已加密。
- 上载新内容
- 为内容编制索引
- 对内容进行编码
- 播放内容
- 发布/取消发布内容


## <a id="upload"></a>如何：上载内容 


1. 在[管理门户](http://manage.windowsazure.cn/)中，单击"Media Services"，然后单击 Media Services 帐户名。
2. 选择"内容"页。 
3. 单击该页上或者门户底部的"上载"按钮。 
4. 在"上载内容"对话框中，浏览到所需的资产文件。单击该文件，然后单击"打开"或按 **Enter**。

	![UploadContentDialog][uploadcontent]

5. 在"上载内容"对话框中，单击勾选按钮以接受文件和内容名称。
6. 随后将开始上载，你可以从门户底部跟踪进度。  

	![JobStatus][status]

上载完成后，内容列表中会列出新的资产。根据约定，名称的末尾将附加"**-Source**"，以便将新内容作为编码任务的源内容进行跟踪。

![ContentPage][contentpage]

如果在上载过程停止后未更新文件大小值，请按"同步元数据"按钮。这会将资产文件大小与存储中的实际文件大小同步，并刷新"内容"页上的值。	

## <a id="index"></a>如何：为内容编制索引

使用 Azure Media Indexer，可以使媒体文件内容可供搜索，并为隐藏的字幕和关键字生成全文本脚本。你可以根据下面所示的步骤，使用管理门户为内容编制索引。但是，如果你想要以更大的力度控制文件和索引作业的完成方式，可以使用 Media Services SDK for .NET 或 REST API。有关详细信息，请参阅[使用 Azure Media Indexer 为媒体文件编制索引](https://msdn.microsoft.com/zh-cn/library/azure/dn783455.aspx)。

下面的步骤演示如何使用管理门户为内容编制索引。

1. 选择要编制索引的文件。
	如果此文件类型支持索引，则"内容"页底部将启用"处理"按钮。
1. 按"处理"按钮。
2. 在"处理"对话框中，选择"Azure Media Indexer"处理器。
3. 然后，在"处理"对话框中，填写输入媒体文件的详细**标题**和**说明**信息。
	
	![Process][process]

## <a id="encode"></a>如何：对内容进行编码
**常用预设**
要通过 Internet 传送数字视频，你必须对媒体进行压缩。Media Services 提供了一个 Media Encoder ，可让你指定如何为内容编码（例如，要使用的编解码器、文件格式、分辨率和比特率。） 

使用 Azure Media Services 时最常见的方案之一是将自适应比特率流传送至你的客户端。通过自适应比特率流，客户端可以在视频显示时，根据当前网络带宽、CPU 利用率和其他因素，切换至较高或较低的比特率流。Media Services 支持下列自适应比特率流技术：HTTP 实时流 (HLS)、平滑流、MPEG DASH 和 HDS（仅适用于 Adobe PrimeTime/Access 许可证持有人）。 
Media Services 所提供的动态打包可让你以 Media Services 支持的流格式（MPEG DASH、HLS、Smooth Streaming、HDS）传送自适应比特率 MP4 或平滑流编码内容，而无须重新打包成这些流格式。 

若要使用动态打包，必须执行下列操作：

- 将夹层（源）文件编码或转换成一组自适应比特率 MP4 文件或自适应比特率平滑流文件（本教程稍后将演示编码步骤），  
- 针对你要传送内容的流式处理终结点，获取至少一个按需流式处理单位。有关详细信息，请参阅[如何缩放按需流式处理保留单元](/zh-cn/documentation/articles/media-services-how-to-scale/)。

通过动态打包，你只需要存储及支付一种存储格式的文件，Media Services 将会根据客户端的要求创建并提供适当的响应。 

请注意，除了能够使用动态打包功能以外，点播流保留单元也为你提供可购买的专用流出容量（以 200 Mbps 为增量来购买）。默认情况下，将在共享实例模型中配置按需流式处理，在该模型中，服务器资源（例如，计算、出口容量等）将与所有其他用户共享。若要增加按需流式处理吞吐量，建议购买按需流式处理保留单位。


本部分介绍通过管理门户使用 Azure Media Encoder 为内容编码时可以执行的步骤。

1.  选择要编码的文件。
	如果此文件类型支持编码，则"内容"页底部将启用"处理"按钮。
4. 在"处理"对话框中，选择"Azure Media Encoder"处理器。
5. 选择"编码配置"之一。

	![Process2][process2]

		
	[Azure Media Encoder 的任务预设字符串](https://msdn.microsoft.com/zh-cn/library/azure/dn619392.aspx)主题说明了**自适应流式传输（动态打包）的预设**、**渐进式下载的预设**、**自适应流式传输的传统预设**等类别中各项预设的含义。  


	下面介绍"其他"配置：

	+ **使用 PlayReady 内容保护进行编码**。此预设格式会生成以 PlayReady 内容保护编码的资产。  
	
	
		默认情况下，将使用 Media Services PlayReady 许可证服务。若要指定其他可让客户端从中获取许可证以播放 PlayReady 加密内容的服务，请使用 REST 或 Media Services .NET SDK API。有关详细信息，请参阅[使用静态加密保护内容]()，并设置 Media Encryptor 预设中的 **licenseAcquisitionUrl** 属性。或者，你可以使用动态加密，并根据[使用 PlayReady 动态加密和许可证传送服务](https://msdn.microsoft.com/zh-cn/library/azure/dn783467.aspx )来设置 **PlayReadyLicenseAcquisitionUrl** 属性。 
	+ **在 PC/Mac 上(通过 Flash/Silverlight)播放**。此预设将生成具有以下特征的平滑流式处理资产：使用 AAC 按照 96 kbps 速率进行 CBR 编码的 44.1 kHz（16 位/采样）立体声音频，使用 H.264 Main Profile 按照 6 种位速率（3400 kbps 到 400 kbps）进行 CBR 编码的 720p 视频，以及两个辅助 GOP。
	+ **通过 HTML5 (IE/Chrome/Safari)播放**。此预设将生成具有以下特征的单个 MP4 文件：使用 AAC 按照 128 kbps 速率进行 CBR 编码的 44.1 kHz（16 位/采样）立体声音频，使用 H.264 Main Profile 按照 4500 kbps 速率进行 CBR 编码的 720p 视频。
	+ **在 iOS 设备和 PC/Mac 上播放**。此预置生成与平滑流式资产（如上所述）具有相同特性的资产，但是采用可以将 Apple HLS 流传递到 iOS 设备的格式。 

5. 然后，输入所需的友好输出内容名称或接受默认值。然后，单击勾选按钮开始编码操作，你可以在门户底部跟踪进度。
6. 按"确定"。

	完成编码后，"内容"页将包含已编码的文件。 

	若要查看编码作业的进度，请切换到"作业"页。  


	如果在完成编码后未更新文件大小值，请按"同步元数据"按钮。这会将输出资产文件大小与存储中的实际文件大小同步，并刷新"内容"页上的值。	

## <a id="publish"></a>如何：发布内容

当你发布内容时，系统将提供一个流 URL 或渐进式下载 URL。你的客户端可以使用此 URL 播放视频。

1. 单击要发布的资产。 
2. 然后单击发布按钮。 
	
	在将内容发布到某个 URL 后，可通过能够呈现编码内容的客户端播放器打开该 URL。

 ![PublishedContent][publishedcontent]

## 如何：从门户播放内容

管理门户提供了可用于测试视频的 Media Services 内容播放器。

单击所需的视频内容，然后单击门户底部的"播放"按钮。 
 
只有已发布的内容才能从门户播放。此外，你的浏览器必须支持编码。


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
