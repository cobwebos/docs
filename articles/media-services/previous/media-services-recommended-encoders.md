---
title: 了解 Azure 媒体服务建议的编码器 | Microsoft 文档
description: 了解媒体服务建议的编码器
services: media-services
keywords: 编码;编码器;媒体
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 373ce1d10af87603b1bdd6339c94891187c35d8c
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295362"
---
# <a name="recommended-on-premises-encoders"></a>推荐的本地编码器
使用 Azure 媒体服务实时传送视频流时，可以指定通道接收输入流的方式。 如果您选择要用于实时编码通道上的本地编码器，则编码器应推送高质量单比特率流作为输出。 如果选择使用在本地编码器与直通通道，则编码器应推送多比特率流作为输出，其中所有所需的输出质量。 有关详细信息，请参阅[实时传送视频流使用在本地编码器](media-services-live-streaming-with-onprem-encoders.md)。

Azure 媒体服务建议使用下列将 RTMP 作为输出的实时编码器之一：
- Adobe Flash 媒体实时编码器 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Azure 媒体服务建议使用下列将多比特率片段 MP4（平滑流式处理）作为输出的实时编码器之一：
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco 数字媒体编码器 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> 实时编码器可以将单比特率流发送到直通通道，但并不建议此配置，因为它不允许对客户端进行自适应比特率流式处理。

## <a name="how-to-become-an-on-premises-encoder-partner"></a>如何成为上的本地编码器合作伙伴
作为 Azure 媒体服务在本地编码器合作伙伴，媒体服务通过向企业客户推荐编码器来推广你的产品。 若要成为上的本地编码器合作伙伴，必须验证与媒体服务上的本地编码器的兼容性。 为此，请完成以下验证：

直通通道验证
1. 创建或访问 Azure 媒体服务帐户
2. 创建并启动“直通”通道
3. 配置编码器推送多比特率实时流。
4. 创建已发布的实时事件
5. 运行实时编码器大约 10 分钟
6. 停止实时事件
7. 创建、启动流式处理终结点，使用诸如 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 之类的播放器来观看已存档的资产，以确保播放没有所有质量级别的明显问题（或者，在第 6 步之前的实时会话中，通过预览 URL 进行观看和验证）
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本
9. 在创建每个示例后重置通道状态
10. 为编码器所支持的所有配置重复（具有或无广告信号/字幕/不同编码速度）步骤 3-9

实时编码通道验证
1. 创建或访问 Azure 媒体服务帐户
2. 创建并启动“实时编码”通道
3. 配置编码器推送单比特率实时流。
4. 创建已发布的实时事件
5. 运行实时编码器大约 10 分钟
6. 停止实时事件
7. 创建、启动流式处理终结点，使用诸如 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 之类的播放器来观看已存档的资产，以确保播放没有所有质量级别的明显问题（或者，在第 6 步之前的实时会话中，通过预览 URL 进行观看和验证）
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本
9. 在创建每个示例后重置通道状态
10. 为编码器所支持的所有配置（具有和无广告信号/字幕/不同编码速度）重复步骤 3-9

使用寿命验证
1. 创建或访问 Azure 媒体服务帐户
2. 创建并启动“直通”通道
3. 配置编码器推送多比特率实时流。
4. 创建已发布的实时事件
5. 运行实时编码器持续一周或更长时间
6. 使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 等播放器不时观看实时传送视频流（或存档资产），以确保播放没有明显问题
7. 停止实时事件
8. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本

最后，通过电子邮件 amsstreaming@microsoft.com 将记录的设置和实时存档参数发送到媒体服务。 收到后，媒体服务会对实时编码器中的示例执行验证测试。 有关此过程的任何问题，请联系媒体服务。
