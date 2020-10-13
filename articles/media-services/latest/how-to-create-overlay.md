---
title: 如何使用 Media Encoder Standard 创建覆盖
description: 了解如何使用 Media Encoder Standard 创建覆盖。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267320"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>如何使用 Media Encoder Standard 创建覆盖

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard 允许在现有视频上覆盖图像。 目前支持以下格式：png、jpg、gif 和 bmp。

## <a name="prerequisites"></a>先决条件

* 在示例中收集配置 appsettings.json 文件所需的帐户信息。 如果你不确定怎么做，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)。 Appsettings.json 文件中应有以下值。

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

如果你不熟悉“转换”，建议完成以下活动：

* 阅读[使用媒体服务编码视频和音频](encoding-concept.md)
* 阅读[如何对自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)。 按照那篇文章中的步骤来设置处理转换所需的 .NET，然后返回此处尝试一个覆盖预设示例。
* 请参阅[转换参考文档](/rest/api/media/transforms)。

熟悉了转换后，请下载覆盖示例。

## <a name="overlays-preset-sample"></a>覆盖预设示例

下载[媒体服务覆盖的示例](https://github.com/Azure-Samples/media-services-overlays)开始进行覆盖。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务编码时对视频进行子剪辑 - .NET](subclip-video-dotnet-howto.md)