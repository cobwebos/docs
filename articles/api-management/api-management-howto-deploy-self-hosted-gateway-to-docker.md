---
title: 将自承载 Azure API 管理网关部署到 Docker | Microsoft Docs
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
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768497"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>将 Azure API 管理自承载网关部署到 Docker

本文提供将自承载 Azure API 管理网关部署到 Docker 环境的步骤。

> [!NOTE]
> 自承载网关功能目前以预览版提供。 在预览期间，自承载网关仅在“开发人员”层和“高级”层提供，不额外收费。 “开发人员”层仅限单个自承载网关部署。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 创建 Docker 环境。 [Docker for Desktop](https://www.docker.com/products/docker-desktop) 是用于开发和评估的极佳选项。 有关所有 Docker 版本、其功能以及 Docker 本身的综合文档的信息，请参阅 [Docker 文档](https://docs.docker.com)。
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自托管网关打包为基于 x86-64 的基于 Linux 的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>将自承载网关部署到 Docker

1. 在“设置”下选择“网关”。********
2. 选择要部署的网关资源。
3. 选择**部署**。
4. 请注意，“令牌”文本框中已自动生成使用默认“过期时间”和“机密密钥”值的新令牌。************ 根据需要调整其中的一个或两个值，然后选择“生成”以创建新令牌。****
4. 确保在“部署脚本”下选择“Docker”。********
5. 选择“环境”旁边的“env.conf”链接以下载该文件。********
6. 选择“运行”文本框右侧的“复制”图标，将 Docker 命令保存到剪贴板。********
7. 将该命令粘贴到终端（或命令）窗口。 根据需要调整端口映射和容器名称。 请注意，该命令要求下载的环境文件位于当前目录中。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 执行命令。 该命令指示 Docker 环境使用从 Microsoft 容器注册表下载的自承载网关映像运行容器，并将该容器的 HTTP (8080) 和 HTTPS (8081) 端口映射到主机上的端口 80 和 443。
9. 运行以下命令来检查网关 pod 是否正在运行：
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. 返回 Azure 门户，确认刚刚部署的网关节点是否报告正常状态。

![网关状态](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> 使用 <code>console docker container logs <gateway-name></code> 命令查看自承载网关日志的快照。
>
> 使用 <code>docker container logs --help</code> 命令查看所有日志查看选项。

## <a name="next-steps"></a>后续步骤

* 要了解有关自托管网关的详细信息，请参阅 Azure [API 管理自托管网关概述](self-hosted-gateway-overview.md)。
* [为自承载网关配置自定义域名](api-management-howto-configure-custom-domain-gateway.md)。
