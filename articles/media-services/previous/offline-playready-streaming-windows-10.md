---
title: 配置帐户以对受 PlayReady 保护的内容进行脱机流式处理 - Azure
description: 本文介绍如何配置 Azure 媒体服务帐户，以便对适用于 Windows 10 的 PlayReady 进行脱机流式处理。
services: media-services
keywords: DASH, DRM, Widevine 脱机模式, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: willzhan
ms.openlocfilehash: 7d907e3d06e8c2817b1fc4a30a7f808d06b022ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884704"
---
# <a name="offline-playready-streaming-for-windows-10"></a>适用于 Windows 10 的 PlayReady 脱机流式处理  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [第 3 版](../latest/offline-plaready-streaming-for-windows-10.md)
> * [第 2 版](offline-playready-streaming-windows-10.md)

Azure 媒体服务支持带 DRM 保护的脱机下载/播放。 本文涵盖用于 Windows 10/PlayReady 客户端的对 Azure 媒体服务的脱机支持。 可以通过以下文章了解对 iOS/FairPlay 和 Android/Widevine 设备的脱机模式支持：

- [适用于 iOS 的 FairPlay 脱机流式处理](media-services-protect-hls-with-offline-fairplay.md)
- [适用于 Android 的 Widevine 脱机流式处理](offline-widevine-for-android.md)

## <a name="overview"></a>概述

本节介绍有关脱机模式播放的一些背景，特别是以下问题的原因：

* 在某些国家/地区，Internet 可用性和/或宽带仍然受到限制。 为获得满意的观看体验，用户可能选择首先下载以便能够观看高分辨率的内容。 在此情况下，通常问题不在于网络可用性，而在于受限的网络宽带。 此时，OTT/OVP 提供程序就会请求提供脱机模式支持。
* 在 Netflix 2016 年第 3 季度股东大会上，Netflix 首席执行官 Reed Hastings 表示，下载内容是“经常请求的功能”，“我们对此持开放态度”。
* 某些内容提供程序可能不允许在某个国家/地区边界之外进行 DRM 许可证传送。 如果用户想在国外旅行期间查看内容，需要脱机下载。
 
实现脱机模式所面临的挑战如下：

* 许多播放器、编码器工具都支持 MP4，但 MP4 容器和 DRM 之间不存在绑定；
* 从长远来看，具有 CENC 的 CFF 是趋势。 然而，目前工具/播放机支持生态系统尚未出现。 现在需要一个解决方案。
 
其理念为：具有 H264/AAC 的平滑流式处理 ([PIFF](https://go.microsoft.com/?linkid=9682897)) 文件格式与 PlayReady (AES-128 CTR) 存在绑定。 平滑流式处理 .ismv 文件（假设音频混合在视频中）本身就是 fMP4，可以用于播放。 如果平滑流式处理内容经过 PlayReady 加密，则每个 .ismv 文件会变成受 PlayReady 保护的片段 MP4。 我们可以选择具有首选比特率的 .ismv 文件，并将其重命名为 .mp4 以便下载。

有以下两种方式用于承载 PlayReady 保护的 MP4，以便进行渐进式下载：

* 可以将此 MP4 放入同一容器/媒体服务资产中，并利用 Azure 媒体服务流式处理终结点进行渐进式下载；
* 使用 SAS 定位符直接从 Azure 存储进行渐进式下载，绕过 Azure 媒体服务。
 
可以使用两种类型的 PlayReady 许可证交付：

* Azure 媒体服务中的 PlayReady 许可证交付服务；
* PlayReady 许可证服务器可以托管在任何位置。

下面是两组测试资产，第一组使用 AMS 中的 PlayReady 许可证交付，而第二组使用 Azure VM 上托管的 PlayReady 许可证服务器：

资产 #1：

* 渐进式下载 URL：[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS)：[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

资产 #2：

* 渐进式下载 URL：[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL（本地）：[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

对于播放测试，我们在 Windows 10 上使用了通用 Windows 应用程序。 在 [Windows 10 通用示例](https://github.com/Microsoft/Windows-universal-samples)中，有一个名为 [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)（自适应流式处理示例）的基础播放器示例。 我们只需要添加代码，选择下载的视频并将其用作源，而不是用作自适应流式处理源。 按钮单击事件处理程序中的更改：

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![脱机模式下播放受 PlayReady 保护的 fMP4](./media/offline-playready/offline-playready1.jpg)

由于视频受 PlayReady 保护，屏幕截图将不能包含该视频。

概括起来，我们在 Azure 媒体服务上实现了脱机模式：

* 内容转码和 PlayReady 加密可以在 Azure 媒体服务或其他工具中完成；
* 内容可以托管在 Azure 媒体服务或 Azure 存储中，用于渐进式下载；
* PlayReady 许可证交付可以来自 Azure 媒体服务或其他位置；
* 已准备好的平滑流式处理内容仍然可以通过 DASH 用作联机流式处理或作为 DRM 使用 PlayReady 进行平滑处理。

## <a name="next-steps"></a>后续步骤

[混合 DRM 系统设计](hybrid-design-drm-sybsystem.md)
