---
title: include 文件
description: include 文件
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545742"
---
在“Azure 资源”页（左侧菜单）的“管理”部分（右上方菜单）中，复制“示例查询”URL，然后粘贴到新的浏览器选项卡中  。

终结点 URL 的格式如下所示，其中 APP-ID 和 KEY-ID 将替换为你自己的自定义子域、应用 ID 和终结点密钥：

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```