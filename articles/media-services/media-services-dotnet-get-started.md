---
title: "使用 .NET 按需传送内容入门 | Microsoft Docs"
description: "本教程会引导完成使用 Azure 媒体服务和 .NET 实施点播内容传送应用程序的步骤。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: f0be787ba1ccee067fb1d7e6a6554be32f886089
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>使用 .NET SDK 开始传送点播内容
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

本教程介绍了在 Azure 媒体服务 .NET SDK 中使用 Azure 媒体服务 (AMS) 应用程序实施基本的视频点播 (VoD) 内容传送服务的步骤。

## <a name="prerequisites"></a>先决条件

以下是完成本教程所需具备的条件：

* 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。
* 一个媒体服务帐户。 若要创建媒体服务帐户，请参阅[如何创建媒体服务帐户](media-services-portal-create-account.md)。
* .NET Framework 4.0 或更高版本。
* Visual Studio。

本教程包括以下任务：

1. 启动流式处理终结点（使用 Azure 门户）
2. 创建和配置 Visual Studio 项目。
3. 连接到媒体服务帐户。
2. 上传视频文件。
3. 将源文件编码为一组自适应比特率 MP4 文件。
4. 发布资产并获取流式处理和渐进式下载 URL。  
5. 播放内容。

## <a name="overview"></a>概述
本教程会引导完成使用用于 .NET 的 Azure 媒体服务 (AMS) SDK 实施视频点播 (VoD) 内容传送应用程序的步骤。

本教程介绍了基本的媒体服务工作流，以及进行媒体服务开发需要用到的最常见编程对象和任务。 完成本教程后，你就能够流式传输或渐进下载你已上传、编码和下载的示例媒体文件。

### <a name="ams-model"></a>AMS 模型

下图显示了在针对媒体服务 OData 模型开发 VoD 应用程序时，某些最常用的对象。

单击图像可查看其完整大小。  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

可以在[此处](https://media.windows.net/API/$metadata?api-version=2.15)查看整个模型。  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>使用 Azure 门户启动流式处理终结点

使用 Azure 媒体服务时，最常见的场景之一是通过自适应比特率流式处理传送视频。 媒体服务提供动态打包，可按媒体服务支持的流格式（MPEG DASH、HLS、平滑流式处理）及时传送自适应比特率 MP4 编码内容，而无需存储上述各种流格式的预打包版本。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。

若要启动流式处理终结点，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)登录。
2. 在“设置”窗口中，单击“流式处理终结点”。
3. 单击默认的流式处理终结点。

    此时会显示“默认流式处理终结点详细信息”窗口。

4. 单击“启动”图标。
5. 单击“保存”按钮保存更改。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

1. 设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 
2. 创建新的文件夹（文件夹可以位于本地驱动器上的任何位置），并复制需要编码和流处理或渐进式下载的 .mp4 文件。 在此示例中，我们使用了“C:\VideoFiles”路径。

## <a name="connect-to-the-media-services-account"></a>连接到媒体服务帐户

使用采用 .NET 的媒体服务时，必须将 **CloudMediaContext** 类用于大多数媒体服务编程任务：连接到媒体服务帐户；创建、更新、访问和删除以下对象：资产、资产文件、作业、访问策略、定位符等等。

使用以下代码覆盖默认程序类。 该代码演示如何从 App.config 文件中读取连接值，以及如何创建 **CloudMediaContext** 对象以连接到媒体服务。 有关详细信息，请参阅[连接到媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。

确保更新保存媒体文件所需的文件名和路径。

**Main** 函数调用会在本部分中进一步定义的方法。

> [!NOTE]
> 将收到编译错误，直到为所有函数添加定义为止。

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>创建新资产并上传视频文件

在媒体服务中，可以将数字文件上传（引入）到资产中。 **资产**实体可以包含视频、音频、图片、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据。）上传文件完成后，相关内容即安全地存储在云中供后续处理和流式处理。 资产中的文件称为 **资产文件**。

下面定义的 **UploadFile** 方法调用 **CreateFromFile**（在 .NET SDK Extensions 中定义）。 **CreateFromFile** 创建指定的源文件所要上传到的新资产。

**CreateFromFile** 方法采用 **AssetCreationOptions**，可用于指定以下任一资产创建选项：

* **无** - 不使用加密。 这是默认值。 请注意，使用此选项时，内容在传送过程中或静态存储过程中都不会受到保护。
  如果计划使用渐进式下载交付 MP4，则使用此选项。
* **StorageEncrypted** - 使用此选项可以通过高级加密标准 (AES) 256 位加密在本地加密明文内容，然后将其上传到 Azure 存储中以加密形式静态存储相关内容。 受存储加密保护的资产会在编码前自动解密并放入经过加密的文件系统中，并可选择在重新上传为新的输出资产前重新加密。 存储加密的主要用例是在磁盘上通过静态增强加密来保护高品质的输入媒体文件。
* **CommonEncryptionProtected** - 上传经过通用加密或 PlayReady DRM 加密并受其保护的内容（例如，受 PlayReady DRM 保护的平滑流）时使用此选项。
* **EnvelopeEncryptionProtected** - 如果要上传使用 AES 加密的 HLS，请使用此选项。 请注意，Transform Manager 必须已对文件进行编码和加密。

**CreateFromFile** 方法还允许指定回调，以报告文件的上传进度。

在以下示例中，指定了 **None** 作为资产选项。

将以下方法添加到 Program 类。

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>将源文件编码为一组自适应比特率 MP4 文件
将资产引入媒体服务后，即可对媒体进行编码、传输复用、打水印等处理，然后将其传送至客户端。 将根据多个后台角色实例调度把那个运行这些活动，以确保较高的性能和可用性。 这些活动称为作业，每个作业由原子任务构成，这些原子任务在资产文件上完成具体的工作。

如前所述，使用 Azure 媒体服务时最常见的方案之一是将自适应比特率流传送至客户端。 媒体服务可以将一组自适应比特率 MP4 文件动态打包为以下其中一种格式：HTTP Live Streaming (HLS)、平滑流式处理和 MPEG DASH。

要利用动态打包，需将夹层（源）文件编码或转换成一组自适应比特率 MP4 文件或自适应比特率平滑流文件。  

以下代码演示如何提交编码作业。 该作业所包含的一项任务会指定要使用 **Media Encoder Standard**将夹层文件转码成一组自适应比特率 MP4。 代码会提交作业，并等待作业完成。

作业完成后，即可流式处理资产，或渐进式下载转码后所创建的 MP4 文件。

将以下方法添加到 Program 类。

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>发布资产并获取用于流式处理和渐进式下载的 URL

要流处理或下载资产，必须先创建定位符来“发布”资产。 定位符提供对资产中所含文件的访问权限。 媒体服务支持两种类型的定位符：用于流媒体（例如 MPEG DASH、HLS 或平滑流式处理）的 OnDemandOrigin 定位符，以及用于下载媒体文件的访问签名 (SAS) 定位符（有关 SAS 定位符的详细信息，请参阅[此](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/)博客）。

### <a name="some-details-about-url-formats"></a>有关 URL 格式的一些详细信息

创建定位符后，可以生成用来流式处理或下载文件的 URL。 本教程中的示例将输出 URL，可以将其粘贴在适当的浏览器中。 此部分直接提供了多个简短的示例，介绍了各种不同的格式。

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>MPEG DASH 的流 URL 采用以下格式：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest**(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>HLS 的流 URL 采用以下格式：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest**(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>平滑流式处理的流 URL 采用以下格式：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>用于下载文件的 SAS URL 采用以下格式：

{blob 容器名称}/{资产名称}/{文件名}/{SAS 签名}

媒体服务.NET SDK 扩展提供了便利的帮助器方法，可针对已发布的资产返回格式化 URL。

以下代码使用 .NET SDK 扩展创建定位符，获取流和渐进式下载 URL。 该代码还演示了如何将文件下载到本地文件夹。

将以下方法添加到 Program 类。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>通过播放内容进行测试

在执行上一部分中定义的程序后，控制台窗口中会显示如下所示的 URL。

自适应流式处理 URL：

平滑流

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

渐进式下载 URL（音频和视频）。

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


要流式传输视频，请将 URL 粘贴到 [Azure 媒体服务播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)的 URL 文本框中。

要测试渐进式下载，请将 URL 粘贴到浏览器（例如 Internet Explorer、Chrome 或 Safari）中。

相关详细信息，请参阅以下主题：

- [使用现有播放器播放内容](media-services-playback-content-with-existing-players.md)
- [开发视频播放器应用程序](media-services-develop-video-players.md)
- [使用 DASH.js 在 HTML5 应用程序中嵌入 MPEG-DASH 自适应流式处理视频](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>下载示例
下面的代码示例包含本教程中创建的代码：[示例](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/
