---
title: 如何使用 Media Encoder Standard 创建覆盖
description: 了解如何使用 Media Encoder Standard 创建覆盖层。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830574"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>如何使用 Media Encoder Standard 创建覆盖

Media Encoder Standard 允许在现有视频上覆盖图像。 目前支持以下格式：png、jpg、gif 和 bmp。

## <a name="prerequisites"></a>必备条件

* 收集在示例中配置*appsettings.js*文件所需的帐户信息。 如果你不确定如何执行此操作，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)。 *appsettings.js文件上*需要以下值。

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
* 请参阅[转换参考文档](/rest/api/media/transforms)。

熟悉转换后，下载叠加示例。

## <a name="overlays-preset-sample"></a>重叠预设示例

下载[媒体服务覆盖示例](https://github.com/Azure-Samples/media-services-overlays)，开始进行覆盖。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务编码时对视频进行子剪辑 - .NET](subclip-video-dotnet-howto.md)