---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建 azure 容器实例 (ACI) 资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455069"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>创建 Azure 容器实例 (ACI) 资源

1. 转到[创建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)容器实例的页面。

2. 上**基础知识**选项卡上，输入以下详细信息：

    |设置|值|
    |--|--|
    |订阅|选择订阅。|
    |资源组|选择可用的资源组或创建一个新如`cognitive-services`。|
    |容器名称|输入一个名称，例如`cognitive-container-instance`。 此名称必须是较低的大写。|
    |Location|选择用于部署的区域。|
    |映像类型|`Public`|
    |映像名称|输入的认知服务容器位置。 这可以是你在中使用的同一位置`docker pull`命令，_例如_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |OS 类型|`Linux`|
    |Size|将大小更改为特定的认知服务容器的建议建议。:<br>2 个内核<br>4 GB

3. 上**网络**选项卡上，输入以下详细信息：

    |设置|值|
    |--|--|
    |端口|将 TCP 端口设置为`5000`。 公开端口 5000 上的容器。|

4. 上**高级**选项卡上，输入以下详细信息中，通过容器[所需的计费设置](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments)到 ACI 资源：

    |高级页密钥|高级页值|
    |--|--|
    |`apikey`|从复制**密钥**文本分析资源页面。 它是不包含空格或短划线的 32 字母数字字符字符串`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|从复制**概述**文本分析资源页面。 示例： `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. 单击**查看和创建**
1. 验证通过后，单击**创建**完成创建过程
1. 已成功部署资源后，便可供使用