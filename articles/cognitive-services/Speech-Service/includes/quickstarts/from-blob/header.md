---
title: 快速入门：识别存储在 Blob 存储中的语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506097"
---
在本快速入门中，你将使用[批量听录 REST API](../../../batch-transcription.md) 识别存储在 [SAS Blob](https://aka.ms/ignite2019/speech/placeholder) 中的语音。 满足几项先决条件后，使用 REST API 识别语音只需几个步骤：
> [!div class="checklist"]
> * 将 JSON 请求发送到语音服务以开始转录语音。
> * 查看听录的状态。
> * 完成后，下载听录结果。