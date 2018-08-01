---
title: Azure 事件网格容器注册表事件架构
description: 介绍为 Azure 事件网格中的容器注册表事件提供的属性
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/20/2018
ms.author: tomfitz
ms.openlocfilehash: 9ed918a7402abcbe79e302421f3b2ac725857464
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188647"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>容器注册表的 Azure 事件网格事件架构

本文提供了容器注册表事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="available-event-types"></a>可用事件类型

Blob 存储发出以下事件类型：

| 事件类型 | Description |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | 推送映像时引发。 |
| Microsoft.ContainerRegistry.ImageDeleted | 删除映像时引发。 |

## <a name="example-event"></a>示例事件

以下示例显示了映像推送事件的架构： 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

映像删除事件的架构与此类似：

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | Blob 存储事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| id | 字符串 | 事件 ID。 |
| timestamp | 字符串 | 发生事件的时间。 |
| action | 字符串 | 包含所提供事件的操作。 |
| 目标 | 对象 | 事件的目标。 |
| 请求 | 对象 | 生成事件的请求。 |
| actor | 对象 | 启动事件的代理。 在大多数情况下，此值可以来自请求的授权上下文。 |
| 源 | 对象 | 生成事件的注册表节点。 换句话说，当执行组件启动事件时，源会生成它。 |

目标对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| mediaType | 字符串 | 引用对象的 MIME 类型。 |
| size | integer | 内容的字节数。 与 Length 字段相同。 |
| digest | 字符串 | 内容摘要，由注册表 V2 HTTP API 规范定义。 |
| length | integer | 内容的字节数。 与 Size 字段相同。 |
| repository | 字符串 | 存储库名称。 |
| url | 字符串 | 指向内容的直接 URL。 |
| 标记 | 字符串 | 标记名称。 |

请求对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| id | 字符串 | 启动事件的请求 ID。 |
| addr | 字符串 | 启动事件的客户端连接的 IP 或主机名可能还有端口。 此值是标准 http 请求中的 RemoteAddr。 |
| host | 字符串 | 注册表实例的外部可访问主机名，由传入请求中的 http 主机标头指定。 |
| 方法 | 字符串 | 生成事件的请求方法。 |
| useragent | 字符串 | 请求的用户代理标头。 |

执行组件对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| 名称 | 字符串 | 与生成事件的请求上下文关联的使用者或用户名。 |

源对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| addr | 字符串 | 生成事件的注册表节点的 IP 或主机名和端口。 通常，此值将由 os.Hostname() 与正在运行的端口一起解析。 |
| instanceID | 字符串 | 正在运行的应用程序实例。 每次重启后更改。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
