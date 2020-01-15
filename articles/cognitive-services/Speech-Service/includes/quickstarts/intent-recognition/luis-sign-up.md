---
title: 快速入门：识别语音、意向和实体，Python - 语音服务
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660452"
---
若要完成意向识别快速入门，需要使用 LUIS 预览门户创建 LUIS 帐户和项目。 本快速入门仅需 LUIS 订阅。 无需语音服务订阅。

你需要做的第一件事是使用 LUIS 预览门户创建 LUIS 帐户和应用。 创建的 LUIS 应用会将预生成域用于主自动化，提供意向、实体和示例言语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点，可使用语音 SDK 进行调用。 

可以按照这些说明创建 LUIS 应用： 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">快速入门：构建预生成域应用</a>

完成后，需要以下三项信息： 

* LUIS 密钥
* LUIS 区域
* LUIS 应用 ID

可以在 [LUIS 预览门户](https://preview.luis.ai/)中从以下位置找到此信息：

1. 在 LUIS 预览版门户中，选择“管理”，然后选择“Azure 资源”   。 在此页上，可以找到你的 LUIS 密钥和位置（有时也称为“区域”）  。  

   > [!div class="mx-imgBorder"]
   > ![LUIS 密钥和位置](../../../media/luis/luis-key-region.png)

2. 获取密钥和位置后，需要应用 ID。 选择“应用程序设置”-- 你的应用 ID 在此页上提供  。

   > [!div class="mx-imgBorder"]
   > ![LUIS 应用 ID](../../../media/luis/luis-app-id.png)