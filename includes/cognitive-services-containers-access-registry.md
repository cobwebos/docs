---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063935"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>使用 Docker CLI 对专用容器注册表进行身份验证

可通过多种方法中的任何一种使用认知服务容器的专用容器注册表进行身份验证，但建议的方法是在 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) 中使用命令行。

使用 [`docker login` 命令](https://docs.docker.com/engine/reference/commandline/login/)（如以下示例所示）登录到 `containerpreview.azurecr.io`，即认知服务容器的专用容器注册表。 将 \<username\> 替换为用户名，将 \<password\> 替换为从 Azure 认知服务团队收到的凭据中提供的密码   。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果已在文本文件中保护了凭据，则可以使用 `cat` 命令将该文本文件的内容连接到 `docker login` 命令，如以下示例所示。 将 \<passwordFile\> 替换为包含密码的文本文件的路径和名称，将 \<username\> 替换为凭据中提供的用户名   。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
