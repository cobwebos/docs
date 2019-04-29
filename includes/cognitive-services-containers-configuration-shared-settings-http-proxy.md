---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 04/01/2019
ms.date: 04/23/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599350"
---
如果需要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

| 名称 | 数据类型 | 描述 |
|--|--|--|
|HTTP_PROXY|string|要使用的代理，例如 `http://proxy:8888`|
|HTTP_PROXY_CREDS|string|向代理验证身份所需的任何凭据，例如 username:password。|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```

