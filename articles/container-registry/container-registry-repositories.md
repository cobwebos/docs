---
title: "Azure 容器注册表存储库 | Microsoft Docs"
description: "如何使用 Azure 容器注册表存储库存储 Docker 映像"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 06b809c31cecef1714f60d04657eb74c611be8cb
ms.contentlocale: zh-cn
ms.lasthandoff: 03/28/2017

---
# <a name="azure-container-registry-repositories"></a>Azure 容器注册表存储库

使用 Azure 容器注册表可将容器映像存储在存储库中。 通过将映像存储在存储库中，可以在隔离环境中获取映像组（或映像版本）。 将映像推送到注册表时，可以指定这些存储库。


## <a name="prerequisites"></a>先决条件
* **Azure 容器注册表** - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 若要将本地计算机设置为 Docker 主机并访问 Docker CLI 命令，请安装 [Docker 引擎](https://docs.docker.com/engine/installation/)。
* **提取映像** - 从公共 Docker Hub 注册表中提取映像，对其进行标记，然后将其推送到注册表。 有关如何推送和提取映像的指导，请参阅[将 Docker 映像推送到 Azure 专用注册表](container-registry-get-started-docker-cli.md)。


## <a name="viewing-repositories-in-the-portal"></a>在门户中查看存储库

将映像推送到容器注册表后，便可以在 Azure 门户中看到托管映像的存储库列表了。

如果按照[将 Docker 映像推送到 Azure 专用注册表](container-registry-get-started-docker-cli.md)一文中的步骤进行操作，现在应在容器注册表中有 Nginx 映像。 在指令中，应指定映像的命名空间。 在以下示例中，该命令将 NGinx 映像推送到“示例”存储库：

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure 容器注册表支持多级存储库命名空间。 使用此功能可将特定应用相关的映像集合分组，或者将特定开发或运营团队的应用集合分组。 若要详细了解容器注册表中的存储库，请参阅 [Azure 中的专用 Docker 容器注册表](container-registry-intro.md)。

若要查看容器注册表存储库，请执行以下操作：

1. 登录到 Azure 门户
2. 在“Azure 容器注册表”边栏选项卡上，选择要检查的注册表
3. 在注册表边栏选项卡中，单击“存储库”以查看所有存储库及其映像的列表
4. （可选）选择要查看标记的特定映像

![门户中的存储库](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>后续步骤
了解基础知识后，便可以开始使用注册表了！ 例如，开始将容器映像部署到 [Azure 容器服务](https://azure.microsoft.com/documentation/services/container-service/)群集。

