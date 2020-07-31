---
title: 如何使用 Media Encoder Standard 创建覆盖
description: 了解如何使用 Media Encoder Standard 创建覆盖层。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433558"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>如何使用 Media Encoder Standard 创建覆盖

Media Encoder Standard 允许在现有视频上覆盖图像。 目前支持以下格式：png、jpg、gif 和 bmp。

## <a name="prerequisites"></a>先决条件

* 收集在示例中配置*appsettings.js*文件所需的帐户信息。 如果你不确定如何执行此操作，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)。 *appsettings.js文件上*需要以下值。

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

如果你尚不熟悉转换，则建议你完成下列活动：

* [通过媒体服务读取编码视频和音频](encoding-concept.md)
* 阅读[如何使用自定义转换-.net 进行编码](customize-encoder-presets-how-to.md)。 按照该文章中的步骤设置使用转换所需的 .NET，然后返回此处尝试使用叠加预设示例。
* 请参阅[转换参考文档](https://docs.microsoft.com/rest/api/media/transforms)。

熟悉转换后，下载叠加示例。

## <a name="overlays-preset-sample"></a>重叠预设示例

下载[媒体服务覆盖示例](https://github.com/Azure-Samples/media-services-overlays)，开始进行覆盖。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务编码时对视频进行子剪辑 - .NET](subclip-video-dotnet-howto.md)