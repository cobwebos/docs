<properties 
	pageTitle="Azure Media Services 概述" 
	description="本部分提供 Azure Media Services 的概述" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>










# Azure Media Services 概述

Microsoft Azure Media Services 是一个可扩展的基于云的平台，使开发人员能够生成可缩放的媒体管理和传送应用程序。Media Services 基于 REST API，你可以使用这些 API 安全地上载、存储、编码和打包视频或音频内容，以供点播以及以实时流形式传送到各种客户端（例如，电视、电脑和移动设备）。

可以完全使用 Media Services 构建端到端工作流。也可以选择使用第三方组件来构建工作流的某些组成部分。例如，使用第三方编码器进行编码。然后，使用 Media Services 进行上载、保护、打包和传送。

若要构建 Media Services 解决方案，你可以使用：

- [Media Services REST API](https://msdn.microsoft.com/zh-cn/library/azure/hh973617.aspx)
- 可用的客户端 SDK 之一：[Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media)、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 现有工具：[Azure 管理门户](http://manage.windowsazure.cn/)或 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)。

**服务级别协议 (SLA)**：Media Services 保证 Media Services 编码的 REST API 事务可实现 99.9% 的可用性。至少订购一个流保留单元时，点播流可顺利以 99.9% 的可用性根据请求提供现有媒体内容服务。可用性基于月度计费周期计算而得。有关详细信息，请下载 [SLA 文档](http://www.windowsazure.cn/support/legal/sla)。

以下海报描绘了 Azure Media Services 的从媒体创建到媒体使用的整个工作流。可以从此处下载该海报：[Azure Media Services 海报](http://www.microsoft.com/download/details.aspx?id=38195)。

![overview][overview]

## 概念

有关详细信息，请参阅[概念](/zh-cn/documentation/articles/media-services-concepts)。

## 选择媒体体验

共享视频内容的前几个步骤之一是确定你的客户端需要哪种类型的体验。你的客户如何观看视频内容？是否要求他们连接到 Internet？它们是在计算机还是手持设备上观看你的内容？你的客户是否期望视频采用高清格式？诸如此类的问题可帮助你为客户带来最佳的体验。

### 访问视频
 
客户可通过四种基本方法访问视频：

- 脱机观看 
- 渐进式下载
- 流式传输\自适应比特率流式传输

#### 脱机观看

若要脱机观看视频，客户需要将整个视频下载到其计算机或设备。视频往往很大，因此可能需要一段时间才能下载完成。脱机观看的好处是将下载到设备后，不再需要建立网络连接就能观看视频。 

#### 渐进式下载

渐进式下载需要客户连接到 Internet，允许他们在下载完整个视频之前就开始观看该视频。脱机查看和渐进式下载方法要求用于观看视频的客户设备具有足够的存储空间来保存整个视频。

#### 流式传输

流式传输技术也要求连接到 Internet，但会不时地下载一小段视频，并在显示后丢弃这个片段。因此，用于观看的设备只需很少的存储空间即可。网络连接的吞吐量可能会有所不同，但无论网络带宽如何，客户仍然有望顺利观看视频。自适应比特率技术允许视频播放器应用程序确定网络条件并从多个比特率中进行选择。当网络通信质量下降时，客户端可以选择较低的比特率，从而让播放器能够继续以较低的视频质量播放视频。当网络条件改善时，客户端可以切换到较高的比特率，以改进视频质量。Azure Media Services 支持下列自适应比特率技术：HTTP 实时流 (HLS)、平滑流、MPEG DASH 和 HDS。

### 使用哪种设备

需要做出的另一个决定是客户将会使用哪种类型的设备来观看视频。Media Services 支持 Web 浏览器、智能手机、平板电脑、XBOX、机顶盒和网络电视。

#### Web 浏览器

Web 浏览器可以在 Windows 电脑、Macintosh 电脑和智能手机上运行。在 Windows 电脑或 Macintosh 电脑上运行时，你可以利用较大的屏幕和较大的存储容量。这样，你便可以流式传输更高质量的视频。在 Windows 电脑或 Macintosh 电脑上，可以使用本机应用程序或 HTML 兼容的 Web 浏览器观看 Media Services 提供的视频。本机应用程序可以支持平滑流、Apple HLS、渐进式下载或脱机观看。HTML5 网页支持渐进式下载。


#### 智能手机

智能手机的屏幕和存储容量都比较小。对于这些设备，流式传输是最佳选择。支持 iPhone、Windows Phone 和 Android 手机。iPhone 与 Android 手机支持平滑流和 HLS。Windows Phone 支持平滑流。

### 平板电脑

平板电脑的屏幕比智能手机要大，但存储容量仍然较小。对于平板电脑，流式传输是最佳选择。存储容量较大的平板电脑也可以利用脱机观看和渐进式下载。

#### XBox

XBox 控制台具有屏幕大、存储容量大的优点，因此适合选择脱机观看、渐进式下载和流式传输。机顶盒和网络电视这些设备的特点也是屏幕大、存储容量小，因此适合流式传输。

### 设备支持的技术

下表显示了 Media Services 支持的每种设备类型和客户端技术：
 
<table border="1">
<tr><th>设备</th><th>技术</th></tr>
<tr><td>iOS</td><td>平滑流、Apple HLS、渐进式下载</td></tr>
<tr><td>Windows Phone 8</td><td>平滑流</td></tr>
<tr><td>Windows RT</td><td>平滑流</td></tr>
<tr><td>Windows</td><td>平滑流、渐进式下载</td></tr>
<tr><td>Android 手机</td><td>平滑流和 Apple HLS</td></tr>
<tr><td>XBox</td><td>平滑流</td></tr>
<tr><td>Macintosh</td><td>Apple HLS、渐进式下载</td></tr>
<tr><td>机顶盒、网络电视</td><td>平滑流、Apple HLS、渐进式下载</td></tr>
</table>


## 常见的开发工作流：视频点播和实时流式处理

### 视频点播：交付流内容 

1. 将优质夹层文件上载到资产中。
1. 编码为自适应比特率 MP4 集。
1. 配置资产传送策略（由动态打包使用）。
1. （可选）配置内容保护和密钥授权策略（由动态加密使用）。
1. 发布资产。
1. 流式传输已发布的内容。 

### 视频点播：为内容编制索引

1. 将优质夹层文件上载到资产中。
1. 为内容编制索引。
1. 使用已编制索引的内容。

### 视频点播：提供渐进式下载 

1. 将优质夹层文件上载到资产中。
1. 编码为自适应比特率 MP4 集或单个 MP4。
1. 发布资产。
1. 渐进式下载内容。

[Media Services 视频点播](/zh-cn/documentation/articles/media-services-video-on-demand-workflow) 包含了介绍如何执行上述任务的主题链接。

### 实时流式处理

1. 创建并启动通道。
1. 检索通道引入 URL。
1. 启动并配置所选的实时转码器。
1. 检索通道的预览终结点，并确认你的通道正确接收实时流。
1. 创建节目和资产。 
2. 为资产配置传送策略（由动态打包使用）。
3. （可选）配置内容保护和密钥授权策略（由动态加密使用）。
1. 若要使流可供用户使用，请创建访问与节目关联的资产所需的流定位符。
1. 在准备好开始流式传输和存档时，启动节目。
1. 在要停止对事件进行流式传输和存档时，停止节目。
1. 删除节目（并选择性地删除资产）。

[Media Services 实时流](/zh-cn/documentation/articles/media-services-live-streaming-wrokflow) 包含了介绍如何执行上述任务的主题链接。

## 客户端

Azure Media Services 提供你所需的工具，以便你创建适用于大多数平台的丰富、动态的客户端播放器应用程序，这些平台包括：iOS 设备、Android 设备、Windows、Windows Phone、Xbox 和机顶盒。

- [平滑流式处理客户端 SDK](http://www.iis.net/downloads/microsoft/smooth-streaming)
- [Microsoft Media Platform：播放器框架](http://playerframework.codeplex.com/) 
- [HTML5 Player Framework 文档](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation)
- [Microsoft Smooth Streaming Plugin for OSMF](https://www.microsoft.com/download/details.aspx?id=36057)
- [Media Player Framework for iOS](https://github.com/Azure/azure-media-player-framework) 
- [授权 Microsoft(r) 平滑流式处理客户端移植工具包](https://www.microsoft.com/mediaplatform/sspk.aspx)
- 在 Windows 8 上生成视频应用程序 
- [XBOX 视频应用程序开发](http://xbox.create.msdn.com/)

有关详细信息，请参阅[开发视频播放器应用程序](/zh-cn/documentation/articles/media-services-develop-video-players)

## 模式与实践指南

[模式与实践指南](https://wamsg.codeplex.com/)
[联机文档](https://msdn.microsoft.com/zh-cn/library/dn735912.aspx)
[可下载的电子书](https://www.microsoft.com/download/details.aspx?id=42629)

## 支持

[Azure 支持](http://www.windowsazure.cn/support/plans/)为 Azure（包括 Media Services）提供支持选项。



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
