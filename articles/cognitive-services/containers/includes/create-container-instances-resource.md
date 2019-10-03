---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure 容器实例资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051170"
---
## <a name="create-an-azure-container-instance-resource"></a>创建 Azure 容器实例资源

1. 中转到容器实例的 "[创建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)" 页。

2. 在“基本信息”选项卡中输入以下详细信息：

    |设置|ReplTest1|
    |--|--|
    |订阅|选择订阅。|
    |资源组|选择可用的资源组, 或创建一个新的资源`cognitive-services`组 (例如)。|
    |容器名称|输入名称, 例如`cognitive-container-instance`。 名称必须在较低的大写。|
    |Location|为部署选择一个区域。|
    |映像类型|如果容器映像存储在不需要凭据的容器注册表中, 请选择`Public`。 如果访问你的容器映像需要凭据, `Private`请选择。 有关容器映像是否为`Public`或`Private` ("公共预览") 的详细信息, 请参阅[容器存储库和映像](../../cognitive-services-container-support.md#container-repositories-and-images)。 |
    |映像名称|输入认知服务容器位置。 该位置是用于`docker pull`命令的参数。 有关可用映像名称及其相应的存储库, 请参阅[容器存储库和映像](../../cognitive-services-container-support.md#container-repositories-and-images)。<br><br>映像名称必须完全限定指定三个部分。 首先, 是容器注册表, 然后是存储库, 最后是映像名称`<container-registry>/<repository>/<image-name>`:。<br><br>下面是一个示例, `mcr.microsoft.com/azure-cognitive-services/keyphrase`它表示 Azure 认知服务存储库下的 Microsoft 容器注册表中的关键短语提取映像。 另一个示例是`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , 它表示容器预览容器注册表的 Microsoft 存储库中的 "语音到文本" 图像。 |
    |OS 类型|`Linux`|
    |Size|将大小更改为特定认知服务容器的建议建议:<br>2 CPU 内核数<br>4 GB

3. 在 "**网络**" 选项卡上, 输入以下详细信息:

    |设置|ReplTest1|
    |--|--|
    |端口|将 TCP 端口设置为`5000`。 在端口5000上公开容器。|

4. 在 "**高级**" 选项卡上, 为 Azure 容器实例资源的容器计费设置输入所需的**环境变量**:

    | Key | ReplTest1 |
    |--|--|
    |`apikey`|从资源的 "**密钥**" 页中复制。 它是一个由 32 个字母数字组成的字符串（不包含空格或短划线），即 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|从资源的 "**概述**" 页中复制。|
    |`eula`|`accept`|

1. 单击 "**查看和创建**"
1. 验证通过后, 单击 "**创建**" 以完成创建过程
1. 成功部署资源后, 已准备就绪