---
title: API 参考 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 此 API 参考提供了有关人员管理、LargePersonGroup/PersonGroup 管理、LargeFaceList/FaceList 管理和人脸算法 API 的信息。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a82bd4b06680c0f7763136af70dcfcae5f10af52
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877196"
---
# <a name="api-reference"></a>API 参考

Azure 人脸 API 是基于云的 API，可提供用于人脸检测和识别的算法。 人脸 API 包含以下类别：

- [人脸算法 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/face):涵盖诸如[检测](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/detectwithstream)、[查找相似](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/findsimilar)、[验证](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/verifyfacetoface)、[识别](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/identify)和[分组](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/group)之类的核心功能。
- [FaceList 管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist)：用来管理用于[查找相似](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/findsimilar)的 FaceList。
- [LargePersonGroup 人员管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/largepersongroupperson)：用来管理用于[识别](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/identify)的 LargePersonGroup 人脸。
- [LargePersonGroup 管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/largepersongroup)：用来管理用于[识别](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/identify)的 LargePersonGroup 数据集。
- [LargeFaceList 管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/largefacelist)：用来管理用于[查找相似](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/findsimilar)的 LargeFaceList。
- [PersonGroup 人员管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroupperson)：用来管理用于[识别](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/identify)的 PersonGroup 人脸。
- [PersonGroup 管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup)：用来管理用于[识别](https://docs.microsoft.com/rest/api/cognitiveservices/face/face/identify)的 PersonGroup 数据集。
- [快照管理 API](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot)：用于管理跨订阅的数据迁移的快照。

<!-- Linguist question: Please confirm that the following are API names and should be left as is: "Person Management, LargePersonGroup/PersonGroup Management, LargeFaceList/FaceList Management, and Face Algorithms" -->
