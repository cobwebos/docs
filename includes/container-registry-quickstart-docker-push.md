---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7ed8b96a528d56b28262936c4b200762b3e93b8e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302235"
---
## <a name="push-image-to-registry"></a>将映像推送到注册表

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如果还没有任何本地容器映像，请运行以下 [docker pull][docker-pull] 命令，从 Docker 中心拉取现有映像。 就此示例来说，请拉取 `hello-world` 映像。

```Docker
docker pull hello-world
```

将映像推送到注册表之前，必须使用 ACR 登录服务器的完全限定的名称进行标记。 登录服务器名称采用 *\<registry-name\>.azurecr.io*（全小写）格式，例如 *mycontainerregistry007.azurecr.io*。

使用 [docker tag][docker-tag] 命令标记映像。 使用 ACR 实例的登录服务器名称替换 `<acrLoginServer>`。

```Docker
docker tag hello-world <acrLoginServer>/hello-world:v1
```

最后，使用 [docker push][docker-push] 将映像推送到 ACR 实例。 使用 ACR 实例的登录服务器名称替换 `<acrLoginServer>`。 此示例创建 **hello-world** 存储库，其中包含 `hello-world:v1` 映像。

```Docker
docker push <acrLoginServer>/hello-world:v1
```

将映像推送到容器注册表后，请从本地 Docker 环境中删除 `hello-world:v1` 映像。 （请注意，此 [docker rmi][docker-rmi] 命令不从 Azure 容器注册表中的 **hello-world** 存储库删除该映像。）

```Docker
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

