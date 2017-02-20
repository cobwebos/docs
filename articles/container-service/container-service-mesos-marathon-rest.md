---
title: "使用 Marathon REST API 管理 Azure DC/OS 群集 | Microsoft Docs"
description: "使用 Marathon REST API 将容器部署到 Azure 容器服务 DC/OS 群集。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: b2b969500d20d0c840f201ed2cf13a6f2ab38ee5
ms.openlocfilehash: 719f1ea6a6f51d4a787f0465a4bbadb1a6057a8b


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>通过 Marathon REST API 管理 DC/OS 容器
DC/OS 提供了一种环境，可进行群集工作负荷的部署和缩放，以及底层硬件的抽象化。 DC/OS 上方是一种管理计划和执行计算工作负荷的框架。 尽管框架可用于许多常见的工作负荷，本文档介绍了如何通过使用 Marathon 创建和缩放容器部署。 

## <a name="prerequisites"></a>先决条件

开始了解这些示例之前，需要一个在 Azure 容器服务中配置的 DC/OS 群集。 还需要具有到此群集的远程连接。 有关这些项目的详细信息，请参阅以下文章：

* [部署 Azure 容器服务群集](container-service-deployment.md)
* [连接到 Azure 容器服务群集](container-service-connect.md)

## <a name="access-the-dcos-apis"></a>访问 DC/OS API
连接到 Azure 容器服务群集后，可通过 http://localhost:local-port 访问 DC/OS 和相关 REST API。 本文档中的示例假定在端口 80 上实现隧道连接。 例如，可以在以 `http://localhost/marathon/v2/` 为开头的 URI 上访问 Marathon 终结点。 

有关 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 和 [Chronos API](https://mesos.github.io/chronos/docs/api.html) 的详细信息，请参阅 Mesosphere 文档；有关 [Mesos 计划程序 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) 的详细信息，请参阅 Apache 文档。

## <a name="gather-information-from-dcos-and-marathon"></a>从 DC/OS 和 Marathon 收集信息
在将容器部署到 DC/OS 群集之前，收集一些有关 DC/OS 的信息，例如 DC/OS 代理的名称和当前状态。 为此，请查询 DC/OS REST API 的 `master/slaves` 终结点。 如果一切正常，则查询会返回 DC/OS 代理列表和每个代理的数个属性。

```bash
curl http://localhost/mesos/master/slaves
```

现在，使用 Marathon `/apps` 终结点检查 DC/OS 群集的当前应用程序部署。 如果是新群集，则会看到应用为一个空数组。

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>部署 Docker 格式容器
使用描述了预期部署的 JSON 文件通过 Marathon 部署 Docker 格式容器。 以下示例部署 Nginx 容器，将 DC/OS 代理的端口 80 绑定到容器的端口 80。 另请注意，`acceptedResourceRoles` 属性设置为 `slave_public`。 这会在面向公众的代理规模集中将容器部署到代理。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

若要部署 Docker 格式的容器，请将 JSON 文件存储在可访问的位置。 接下来，要部署容器，请运行以下命令。 指定 JSON 文件的名称（此示例中为 `marathon.json`）。

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

输出与下面类似：

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

现在，如果查询 Marathon 应用程序，则此新应用程序会出现在输出中。

```bash
curl localhost/marathon/v2/apps
```

可在 `http://<containerServiceName>agents.<region>.cloudapp.azure.com` 上通过向代理池的完全限定域名发出 HTTP 请求来验证 Nginx 是否正在运行。

## <a name="scale-your-containers"></a>缩放容器
可以使用 Marathon API 来扩大或缩小应用程序部署。 在上一示例中，为应用程序部署了一个实例。 让我们进行扩展，扩大为三个实例。 为此，请使用以下 JSON 文本创建 JSON 文件，并将其存储在可访问的位置。

```json
{ "instances": 3 }
```

运行以下命令来扩大应用程序。

> [!NOTE]
> URI 为 http://localhost/marathon/v2/apps/，其后接要缩放的应用程序 ID。 如果使用了此处提供的 Nginx 示例，则 URI 将为 http://localhost/marathon/v2/apps/nginx。
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最后，查询应用程序的 Marathon 终结点。 此时可看到有&3; 个 Nginx 容器。

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>等效的 PowerShell 命令
可以在 Windows 系统上使用 PowerShell 命令执行这些操作。

若要收集有关该 DC/OS 群集的详细信息，如代理名称和代理状态，请运行以下命令：

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

使用描述了预期部署的 JSON 文件通过 Marathon 部署 Docker 格式容器。 以下示例部署 Nginx 容器，将 DC/OS 代理的端口 80 绑定到容器的端口 80。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

若要部署 Docker 格式的容器，请将 JSON 文件存储在可访问的位置。 接下来，要部署容器，请运行以下命令。 指定 JSON 文件的名称（此示例中为 `marathon.json`）。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

还可以使用 Marathon API 来扩大或缩小应用程序部署。 在上一示例中，为应用程序部署了一个实例。 让我们进行扩展，扩大为三个实例。 为此，请使用以下 JSON 文本创建 JSON 文件，并将其存储在可访问的位置。

```json
{ "instances": 3 }
```

运行以下命令来扩大应用程序：

> [!NOTE]
> URI 为 http://localhost/marathon/v2/apps/，其后接要缩放的应用程序 ID。 如果使用了此处提供的 Nginx 示例，则 URI 将为 http://localhost/marathon/v2/apps/nginx。
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>后续步骤
* [阅读有关 Mesos HTTP 终结点的详细信息](http://mesos.apache.org/documentation/latest/endpoints/)
* [阅读有关 Marathon REST API 的详细信息](https://mesosphere.github.io/marathon/docs/rest-api.html)




<!--HONumber=Feb17_HO1-->


