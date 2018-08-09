---
title: 获取 Azure 上自定义语音服务的订阅密钥 | Microsoft Docs
description: 了解如何获取调用认知服务中自定义语音服务所需的订阅密钥。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d3969114323f5675c5e14ab36990b124e84ead37
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427561"
---
# <a name="obtain-subscription-keys"></a>获取订阅密钥
若要开始使用 Azure 自定义语音服务，首先需要将用户帐户关联到 Azure 订阅。 订阅适用于免费层和付费层。 若要了解层，请参阅[定价页](https://www.microsoft.com/cognitive-services/en-us/pricing)。

## <a name="get-a-subscription-key"></a>获取订阅密钥
1. 可以通过下面两种方式之一，从 Azure 门户获取订阅密钥：

    * 转到 [Azure 门户](https://ms.portal.azure.com)，通过搜索“认知服务”添加新认知服务 API，再选择“认知服务 API”。

      ![搜索“认知服务”](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * 或直接转到[“认知服务 API”](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices)。

        ![认知服务 API](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. 填写以下必填字段：

      a. **帐户名**。 命名适合自己的名称。 请记住此名称，这样才能在资源列表中找到认知服务订阅。

      b. **订阅**。 选择 Azure 订阅之一。

      c. **API 类型**。 选择“自定义语音服务(预览)”。

      d. **位置**。 暂为“美国西部”。

      e. **定价层**。 选择适合自己的定价层。 F0 是免费层。 [定价页](https://www.microsoft.com/cognitive-services/en-us/pricing)中介绍了允许的配额。

      ![创建认知服务帐户](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. 应使用所提供的帐户名在资源列表中找到仪表板视图或服务。 选择它后，就可以概览服务。 在左侧列表的“资源管理”下，选择“密钥”。 复制“密钥 1”。

      在后续步骤中，需要用到此订阅密钥。

      ![密钥 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > 不要复制“概览”页上的“订阅 ID”。 在下一步中，需要用到此订阅密钥。
      >

      ![“概览”页上的“订阅 ID”](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. 若要输入订阅密钥，请选择功能区右上角的用户帐户。 选择下拉菜单中的“订阅”。

      ![“订阅”菜单项](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    此时，订阅表显示（首次打开时为空）。

    ![订阅表](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. 选择“添加新订阅”。 输入订阅名称和订阅密钥。 可以是订阅中的“密钥 1”（主密钥）或“密钥 2”（辅助密钥）。

      ![订阅密钥名称](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

若要上传数据、定型模型或执行部署，需要将自定义语音服务活动关联到 Azure 订阅。 可以是免费层订阅，也可以是付费层订阅。 有关详细信息，请参阅[定价页](https://www.microsoft.com/cognitive-services/en-us/pricing)。

## <a name="get-a-subscription-id"></a>获取订阅 ID
若要获取订阅 ID，请转到 Azure 门户。 搜索“认知服务”和“自定义语音服务”，并按照说明操作。

> [!NOTE]
> 在此过程的后续步骤中，需要用到订阅密钥。
>

## <a name="next-steps"></a>后续步骤
* 开始创建[自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)。
* 开始创建[自定义语言模型](cognitive-services-custom-speech-create-language-model.md)。
