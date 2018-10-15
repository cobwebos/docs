---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019076"
---
1. 打开 Visual Studio 2017 Community Edition。
1. 创建一个新的“控制台应用(.Net Core)”项目并将项目命名为 `QnaMakerQuickstart`。 接受其余设置的默认值。
1. 在“解决方案资源管理器”中，右键单击项目名称 **QnaMakerQuickstart**，并选择“管理 NuGet 包...”。
1. 在 NuGet 窗口中，选择“浏览器”，然后搜索 **Newtonsoft.JSON** 并安装该包。 该包用于分析 QnaMaker HTTP 响应中返回的 JSON。 