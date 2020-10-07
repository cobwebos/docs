---
title: Azure Media Player 概述
description: 详细了解 Azure Media Player。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87011697"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 概述 #

Azure Media Player 是一种 Web 视频播放器，用于在各种浏览器和设备中播放 [Microsoft Azure 媒体服务](https://azure.microsoft.com/services/media-services/)中的媒体内容。 Azure 媒体播放器采用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。  如果无法在设备或浏览器中提供这些标准，Azure 媒体播放器会采用 Flash 和 Silverlight 作为回退技术。 如果不考虑所使用的播放技术，开发人员将有一个统一的 JavaScript 接口来访问 API。  这使 Azure 媒体服务提供的内容无需其他措施便可在各种设备和浏览中轻松播放。

Microsoft Azure 媒体服务允许播放使用 DASH、平滑流式处理和 HLS 流格式提供的内容。 Azure 媒体播放器会考虑这些不同的格式并基于平台/浏览器功能自动播放最佳链接。 Microsoft Azure 媒体服务还允许使用通用加密（PlayReady 或 Widevine）或 AES-128 位信封加密对资产进行动态加密。 Azure 媒体播放器在合理配置时允许对 PlayReady 和 AES-128 位加密的内容进行解密。  请参阅[受保护内容](azure-media-player-protected-content.md)部分了解如何配置此内容。

若要请求新功能，请将意见或反馈提交至 [UserVoice for Azure Media Player](https://aka.ms/ampuservoice)。 如有具体问题、疑问或发现任何 bug，请通过 ampinfo@microsoft.com 与我们联系。

请进行[注册](https://aka.ms/ampsignup)，以便不错过任何发布，并及时获取 Azure Media Player 所提供的最新功能。

> [!NOTE]
> 请注意，Azure Media Player 仅支持来自 Azure 媒体服务的媒体流。

## <a name="license"></a>许可 ##

Azure Media Player 已获得许可，并受适用于 Azure Media Player 的 Microsoft 软件许可条款中列出的条款约束。 有关完整条款，请参阅[许可证文件](/legal/azure-media-player/azure-media-player-license)。 有关详细信息，请参阅[隐私声明](https://www.microsoft.com/en-us/privacystatement/default.aspx)。

版权所有 2015 Microsoft Corporation。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
