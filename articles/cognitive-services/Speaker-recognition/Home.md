---
title: 说话人辨识 API | Microsoft Docs
description: 将用于说话人验证和说话人识别的先进算法与认知服务中的说话人辨识 API 结合使用。
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365656"
---
# <a name="speaker-recognition-api"></a>说话人识别 API

欢迎使用 Microsoft 说话人辨识 API。 说话人辨识 API 是基于云的 API，提供用于说话人验证和说话人识别的最先进算法。 说话人辨识可以分为两类：说话人验证和说话人识别。


## <a name="speaker-verification"></a>说话人验证


语音的独特特征可用于辨识一个人，就像指纹一样。  使用语音作为访问控制和身份验证方案的信号已成为一种全新创新工具，本质上提升了安全级别，从而简化了客户的身份验证体验。

说话人验证 API 可以使用声音或语音自动验证用户身份。

### <a name="enrollment"></a>注册

说话人验证注册依赖文本。也就是说，说话人需要选择特定的通行短语，以用于注册和验证阶段。 

在注册过程中，将会记录用户说出某短语的声音，然后提取大量特征，并识别选定短语。 提取的特征和选定短语共同构成唯一语音签名。

### <a name="verification"></a>验证
###
在验证过程中，将把输入语音和短语与注册语音签名和短语进行比较，以验证是否是由同一个人说出，以及所说的短语是否正确。

若要详细了解说话人验证，请参阅 API [说话人 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)。

## <a name="speaker-identification"></a>说话人识别

说话人辨识 API 可以自动识别音频文件中的说话人（来自一组给定的预测说话人）。 将把输入音频与提供的一组说话人进行配对。如果发现匹配，就会返回说话人的身份。

所有说话人都应先完成注册过程，才能将语音注册到系统中，并创建语音打印。


### <a name="enrollment"></a>注册

说话人识别注册不依赖文本。也就是说，对说话人在音频中所说的内容没有限制。 记录说话人的语音，并提取大量特征，以构成唯一语音签名。 


### <a name="recognition"></a>识别

在识别过程中提供的是，未知说话人的语音以及一组预测说话人。 将输入语音与所有说话人进行比较，以确定这是谁的声音。如果发现匹配，就会返回说话人的身份。


若要详细了解说话人识别，请参阅 API [说话人 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)。
