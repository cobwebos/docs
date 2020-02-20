---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279707"
---
在“管理”部分（右上方菜单），“Azure 资源”页（左侧菜单）上复制“示例查询”URL，然后粘贴到新的浏览器选项卡中    。

终结点 URL 的格式如下所示，其中 APP-ID 和 KEY-ID 将替换为你自己的应用 ID 和终结点密钥：

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```