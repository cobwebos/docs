---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588766"
---
在“Azure 资源”页（左侧菜单）的“管理”部分（右上方菜单）中，复制“示例查询”URL，然后粘贴到新的浏览器选项卡中  。

终结点 URL 的格式如下所示，其中 APP-ID 和 KEY-ID 将替换为你自己的自定义子域、应用 ID 和终结点密钥：

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```