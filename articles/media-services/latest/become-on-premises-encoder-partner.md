---
title: 成为本地编码器合作伙伴-Azure 媒体服务
description: 本文介绍如何验证本地实时流式处理编码器。
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298625"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>如何验证本地实时流式处理编码器

作为 Azure 媒体服务本地编码器合作伙伴，媒体服务通过向企业客户推荐编码器来推广你的产品。 若要成为本地编码器合作伙伴，必须验证本地编码器与媒体服务的兼容性。 为此，请完成以下验证。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>直通直播活动验证

1. 在媒体服务帐户中，确保**流式处理终结点**正在运行。 
2. 创建并启动直通直播活动。 <br/> 有关详细信息，请参阅[直播活动状态和计费](live-event-states-billing.md)。
3. 获取引入 URL 并配置本地编码器以使用 URL 将多比特率实时流发送到媒体服务。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。
6. 创建**实时输出**并使用创建的资产名称。
7. 使用内置的流式处理策略类型创建流式处理定位符。
8. 列出流式处理定位器的路径，以取回要使用的 URL。
9. 获取要从中流式传输的**流式处理终结点**的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 运行实时编码器大约 10 分钟。
12. 停止直播活动。 
13. 使用 [Azure Media Player](https://aka.ms/azuremediaplayer) 等播放器观看存档的资产，确保以各种质量水平播放时不会出现明显的问题。 或者，在实时会话中通过预览 URL 进行观看和验证。
14. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
15. 在创建每个示例后重置直播活动状态。
16. 对编码器支持的所有配置重复（有或无广告信号、字幕或不同编码速度）步骤 5 到 15。

## <a name="live-encoding-live-event-verification"></a>实时编码直播活动验证

1. 在媒体服务帐户中，确保**流式处理终结点**正在运行。 
2. 创建并启动实时编码直播活动。 <br/> 有关详细信息，请参阅[直播活动状态和计费](live-event-states-billing.md)。
3. 获取引入 URL，并配置编码器以将单比特率实时流推送到媒体服务。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。
6. 创建**实时输出**并使用创建的资产名称。
7. 使用内置的流式处理策略类型创建流式处理定位符。
8. 列出流式处理定位器的路径，以取回要使用的 URL。
9. 获取要从中流式传输的**流式处理终结点**的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 运行实时编码器大约 10 分钟。
12. 停止直播活动。
13. 使用 [Azure Media Player](https://aka.ms/azuremediaplayer) 等播放器观看存档的资产，确保以各种质量水平播放时不会出现明显的问题。 或者，在实时会话中通过预览 URL 进行观看和验证。
14. 记录资产 ID、为实时存档发布的流式处理 URL，以及实时编码器所使用的设置和版本。
15. 在创建每个示例后重置直播活动状态。
16. 对编码器支持的所有配置重复（有或无广告信号、字幕或不同编码速度）步骤 5 到 15。

## <a name="longevity-verification"></a>使用寿命验证

遵循[直通直播活动验证](#pass-through-live-event-verification)中的相同步骤（步骤 11 除外）。 <br/>运行实时编码器一周或更长时间，而不是 10 分钟。 使用 [Azure Media Player](https://aka.ms/azuremediaplayer) 等播放器不时观看实时传送视频流（或存档的资产），确保播放没有明显问题。

## <a name="email-your-recorded-settings"></a>通过电子邮件发送记录的设置

最后，通过电子邮件将记录的设置和实时存档参数作为通知发送到 Azure 媒体服务 (amshelp@microsoft.com)，告知所有自我验证检查已通过。 另请提供联系信息，以方便跟进。 在此过程中如有任何问题，可以联系 Azure 媒体服务团队。

## <a name="see-also"></a>另请参阅

[测试的本地编码器](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>后续步骤

[使用媒体服务 v3 实时传送视频流](live-streaming-overview.md)
