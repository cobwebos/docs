---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286248"
---
如果需要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

| 名称 | 数据类型 | 描述 |
|--|--|--|
|HTTP_PROXY|string|要使用的代理，例如 `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|对代理进行身份验证所需的凭据，例如 username:password。|
|`<proxy-user>`|string|代理的用户。|
|`proxy-password`|string|与代理的 `<proxy-user>` 关联的密码。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
