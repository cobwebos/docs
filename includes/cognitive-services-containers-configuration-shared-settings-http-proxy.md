---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88639038"
---
如果需要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

| 名称 | 数据类型 | 说明 |
|--|--|--|
|HTTPS_PROXY|字符串|要使用的代理，例如 `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|字符串|对代理进行身份验证所需的凭据，例如 username:password。|
|`<proxy-user>`|字符串|代理的用户。|
|`<proxy-password>`|字符串|与代理的 `<proxy-user>` 关联的密码。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
