---
title: 开发视频播放器应用程序
description: 本主题提供了可用于开发自己的客户端应用程序的播放器框架和插件的链接，这些应用程序可以使用媒体服务中的流媒体。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906822"
---
# <a name="develop-video-player-applications"></a>开发视频播放器应用程序
## <a name="overview"></a>概述
Azure 媒体服务提供了为大多数平台创建丰富的动态客户端播放器应用程序所需的工具，包括： iOS 设备、Android 设备、Windows、Windows Phone、Xbox 和机顶盒。 本主题还提供了 Sdk 和播放器框架的链接，可用于开发自己的客户端应用程序，这些应用程序可以使用 Azure 媒体服务中的流媒体。

>[!NOTE]
>创建 AMS 帐户后，会将**默认**流式处理终结点添加到帐户的 "**已停止**" 状态。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于 "**正在运行**" 状态。 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo)是一种 web 视频播放器，用于在各种浏览器和设备上播放 Microsoft Azure 媒体服务的媒体内容。 Azure Media Player 利用行业标准（如 HTML5、媒体源扩展（MSE）和加密媒体扩展（EME））提供丰富的自适应流式处理体验。 如果这些标准在设备或浏览器中不可用，Azure Media Player 将使用 Flash 和 Silverlight 作为回退技术。 无论使用何种播放技术，开发人员都可以使用统一的 JavaScript 接口来访问 Api。 这使 Azure 媒体服务提供的内容可在各种设备和浏览器中播放，无需任何额外的工作。

Microsoft Azure 媒体服务允许通过破折号、平滑流式处理和 HLS 流格式提供内容来播放内容。 Azure Media Player 会考虑这些不同的格式，并根据平台/浏览器功能自动播放最佳链接。 Microsoft Azure 媒体服务还允许通过 PlayReady 加密或 AES-128 位信封加密对资产进行动态加密。 Azure Media Player 允许对 PlayReady 和 AES-128 位加密内容进行适当配置时进行解密。 

有关详细信息：

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player 文档](https://aka.ms/ampdocs) 
* [Azure Media Player 入门博客](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [注册以随时了解最新的 Azure Media Player](https://aka.ms/ampsignup)
* [添加新的功能请求、想法和反馈](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>用于创建播放器应用程序的其他工具
你还可以使用以下任一 Sdk：

* [平滑流式处理客户端 SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [平滑流式处理 Windows 应用商店应用程序](media-services-build-smooth-streaming-apps.md)
* [Microsoft 媒体平台：播放器框架](https://playerframework.codeplex.com/) 
* [HTML5 Player Framework 文档](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [适用于 OSMF 的 Microsoft 平滑流式处理插件](https://www.microsoft.com/download/details.aspx?id=36057) 
* [授权 Microsoft®平滑流式处理客户端移植工具包](https://aka.ms/sspk) 
* [XBOX 视频应用程序开发](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>广告
Azure 媒体服务通过 Windows Media 平台提供广告插入支持：播放器框架。 带有广告支持的播放器框架适用于 Windows 8、Silverlight、Windows Phone 8 和 iOS 设备。 每个播放器框架都包含演示如何实现播放器应用程序的示例代码。 有三种不同类型的广告可插入媒体：

线性-暂停主视频的全帧广告

非线性-播放主视频时显示的叠加广告，通常是放置在播放机内的徽标或其他静态图像

伴生–在播放机之外显示的广告

广告可置于主视频时间线中的任意位置。 你必须告知播放器何时播放广告以及播放哪些广告。 这是使用一组标准的基于 XML 的文件完成的：视频广告服务模板（大型）、数字视频多广告播放列表（VMAP）、媒体抽象排序模板（MAST）和数字视频播放器广告接口定义（VPAID）。 大型文件指定要显示的广告。 VMAP 文件用于指定何时播放各种广告并包含大量 XML。 MAST 文件是对广告进行排序的另一种方法，也可以包含大量的 XML。 VPAID files 定义视频播放器与广告或广告服务器之间的接口。 有关详细信息，请参阅[插入广告](https://msdn.microsoft.com/library/dn387398.aspx)。

有关实时流式处理视频中隐藏字幕和广告支持的信息，请参阅[支持的隐藏字幕和广告插入标准](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另請參閱
[使用短线将 MPEG 虚线自适应流式处理视频嵌入到 HTML5 应用程序中](media-services-embed-mpeg-dash-in-html5.md)

[GitHub 短线存储库](https://github.com/Dash-Industry-Forum/dash.js)

