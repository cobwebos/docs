---
title: 将自承载 Azure API 管理网关部署到 Docker |Microsoft Docs
description: 了解如何将自承载 Azure API 管理网关部署到 Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: fc7e0f7e4e0e80a030a437bc4f0f13360595f32e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747705"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>将 Azure API 管理的自承载网关部署到 Docker

本文提供了将自承载 Azure API 管理网关部署到 Docker 环境的步骤。

> [!NOTE]
> 自承载网关功能处于预览阶段。 在预览期间，仅在开发人员和高级层提供自承载的网关，无额外费用。 开发人员层限制为单个自行托管的网关部署。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 创建 Docker 环境。 [Docker For Desktop](https://www.docker.com/products/docker-desktop)是一种用于开发和评估目的的好方法。 有关 docker 的所有版本、功能以及 Docker 本身的综合文档，请参阅[docker 文档](https://docs.docker.com)。
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自承载的网关打包为基于 Linux 的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>将自承载网关部署到 Docker

1. 从 "**设置**" 下选择 "**网关**"。
2. 选择要部署的网关资源。
3. 选择 "**部署**"。
4. 请注意，"**令牌**" 文本框中的新令牌是使用默认的 "**过期** **" 和 "密钥"** 值自动生成的。 如果需要，请调整其中一个或两个，然后选择 "**生成**" 以创建新令牌。
4. 请确保在**部署脚本**下选择**Docker** 。
5. 选择**环境**旁边的 " **env**文件" 链接，以下载该文件。
6. 选择位于 "**运行**" 文本框右端的 "**复制**" 图标，将 Docker 命令保存到剪贴板。
7. 将命令粘贴到终端（或命令）窗口。 根据需要调整端口映射和容器名称。 请注意，该命令要求当前目录中存在下载的环境文件。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 执行此命令。 命令指示 Docker 环境运行容器，使用从 Microsoft 容器注册表下载的自承载网关的映像，并将容器的 HTTP （8080）和 HTTPS （8081）端口映射到主机上的端口80和443。
9. 运行以下命令以检查网关 pod 是否正在运行：
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. 返回 Azure 门户，确认刚部署的网关节点正在报告运行状态。

![网关状态](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> 使用 <code>console docker container logs <gateway-name></code> 命令查看自承载网关日志的快照。
>
> 使用 <code>docker container logs --help</code> 命令查看所有日志查看选项。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅[AZURE API 管理自承载网关概述](self-hosted-gateway-overview.md)。
* [为自承载网关配置自定义域名](api-management-howto-configure-custom-domain-gateway.md)。
