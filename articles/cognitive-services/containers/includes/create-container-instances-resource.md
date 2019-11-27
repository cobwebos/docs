---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure 容器实例资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383512"
---
## <a name="create-an-azure-container-instance-resource"></a>创建 Azure 容器实例资源

1. 转到容器实例的[创建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)页。

2. 在“基本信息”选项卡中输入以下详细信息：

    |设置|值|
    |--|--|
    |订阅|选择订阅。|
    |资源组|选择可用的资源组，或者创建一个新的，例如 `cognitive-services`。|
    |容器名称|输入一个名称，例如 `cognitive-container-instance`。 此名称必须小写。|
    |位置|选择要部署的区域。|
    |映像类型|如果容器映像存储在不需要凭据的容器注册表中，请选择 "`Public`"。 如果访问容器映像需要凭据，请选择 "`Private`"。 有关容器映像是否 `Public` 或 `Private` （"公共预览"）的详细信息，请参阅[容器存储库和映像](../../cognitive-services-container-support.md#container-repositories-and-images)。 |
    |映像名称|输入认知服务容器位置。 位置是 `docker pull` 命令的参数。 有关可用映像名称及其相应的存储库，请参阅[容器存储库和映像](../../cognitive-services-container-support.md#container-repositories-and-images)。<br><br>映像名称必须完全限定指定三个部分。 首先，是容器注册表，然后是存储库，最后是映像名称： `<container-registry>/<repository>/<image-name>`。<br><br>下面是一个示例，`mcr.microsoft.com/azure-cognitive-services/keyphrase` 会在 Azure 认知服务存储库下的 Microsoft 容器注册表中表示关键短语提取映像。 另一个示例是，`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 表示容器预览容器注册表的 Microsoft 存储库中的 "语音到文本" 图像。 |
    |OS 类型|`Linux`|
    |大小|对于特定的认知服务容器，请将大小更改为建议的设置：<br>2 个CPU 核心<br>4 GB

3. 在“网络”选项卡上，输入以下详细信息：

    |设置|值|
    |--|--|
    |端口|将 TCP 端口设置为 `5000`。 在端口 5000 上公开此容器。|

4. 在 "**高级**" 选项卡上，为 Azure 容器实例资源的容器计费设置输入所需的**环境变量**：

    | Key | 值 |
    |--|--|
    |`apikey`|从资源的“键”页复制。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|从资源的“概览”页复制。|
    |`eula`|`accept`|

1. 单击“查看并创建”
1. 验证通过后，单击“创建”，完成创建过程
1. 成功部署资源以后，即可使用它