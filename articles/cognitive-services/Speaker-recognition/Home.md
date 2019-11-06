---
title: 什么是说话人识别 API？
titleSuffix: Azure Cognitive Services
description: 使用认知服务中的说话人识别 API 进行说话人验证和说话人识别。
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464918"
---
# <a name="speaker-recognition-api---preview"></a>说话人识别 API - 预览版

说话人识别 API 是基于云的 API，提供用于说话人验证和说话人识别的先进 AI 算法。 说话人识别分为两个类别：说话人验证和说话人识别。

## <a name="speaker-verification"></a>说话人验证

语音具有可与个人关联的独特特征。  在呼叫中心和 Web 服务等场景中，应用程序可以使用语音作为附加的验证因素。

说话人验证 API 充当一个智能工具，可以帮助使用用户的声音和语音通行短语来验证其身份。

### <a name="enrollment"></a>注册

说话人验证注册依赖文本。也就是说，说话人需要选择特定的通行短语，以用于注册和验证阶段。

在说话人注册阶段，说话人需要讲出特定的短语，系统会对其录音。 然后提取语音特征来构成唯一的语音签名，同时识别所选的短语。 这些说话人注册数据共同用于验证说话人的身份。 说话人注册数据存储在受保护的系统中。 这些数据的保留时长由客户控制。 客户可以通过 API 调用创建、更新和删除说话人的个人注册数据。  删除订阅后，与该订阅关联的所有说话人注册数据也会一并删除。

客户应确保已收到要进行说话人验证的用户提供的相应权限。

### <a name="verification"></a>验证

在验证阶段，客户应结合与要验证的个人关联的 ID 调用说话人验证 API。  服务从输入的录音中提取语音特征和通行短语。 然后，它会将这些特征与客户想要验证的说话人的说话人注册数据的相应元素进行比较，并确定是否存在任何匹配项。  响应中会返回“接受”或“拒绝”以及不同的置信度。  然后，客户确定如何使用结果来帮助判断此人是否为已注册的说话人。

应该根据场景以及所用的其他验证因素设置阈值置信度。 我们建议试验该置信度，并考虑每个应用场景的适当设置。 这些 API 并不旨在用于确定音频是来自真实的个人、模仿内容，还是已注册的说话人的录音。

在验证阶段，服务不会保留录音，也不会保留已发送到服务的提取语音特征。

若要详细了解说话人验证，请参阅 API [说话人 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)。

## <a name="speaker-identification"></a>说话人识别

在给定了一组已注册说话人的情况下，应用程序可以使用语音来识别“谁正在说话”。 可以在生产力会议、个性化和呼叫中心听录等场景中使用说话人识别 API。

### <a name="enrollment"></a>注册

说话人识别注册不依赖文本。也就是说，对说话人在音频中所说的内容没有限制。 无需通行短语。

在注册阶段，将为说话人录音，并提取语音特征来构成唯一的语音签名。 提取的语音音频和特征存储在受保护的系统中。 这些信息的保留时长由客户控制。 客户可以通过 API 调用创建、更新和删除说话人个人的这些注册数据。 删除订阅后，与该订阅关联的所有说话人注册数据也会一并删除。

客户应确保已收到要进行说话人识别的用户提供的相应权限。

### <a name="identification"></a>识别

在识别阶段，说话人识别服务将从输入的录音中提取语音特征。 然后，它将这些特征与指定的说话人列表中的注册数据进行比较。 如果找到了已注册说话人之间的匹配项，响应中会返回该说话人的 ID 以及置信度。  否则，如果未找到与已注册说话人之间的匹配项，响应中会返回“拒绝”。

应该根据场景设置阈值置信度。 我们建议试验该置信度，并考虑每个应用场景的适当设置。 这些 API 并不旨在用于确定音频是来自真实的个人、模仿内容，还是已注册的说话人的录音。

在识别阶段，服务不会保留录音，也不会保留已发送到服务的提取语音特征。

若要详细了解说话人识别，请参阅 API [说话人 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)。
