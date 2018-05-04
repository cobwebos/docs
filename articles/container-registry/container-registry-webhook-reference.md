---
title: Azure 容器注册表 Webhook 架构参考
description: Azure 容器注册表的 Webhook 请求 JSON 有效负载参考。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-webhook-reference"></a>Azure 容器注册表 Webhook 参考

可以为容器注册表[配置 Webhook](container-registry-webhook.md)，以便对其执行某些操作时生成相应事件。 例如，可以启用在容器映像 `push` 和 `delete` 操作上触发的 Webhook。 触发 Webhook 后，Azure 容器注册表向指定的终结点发出 HTTP 或 HTTPS 请求并包含有关此事件的信息。 然后终结点处理相应的 Webhook 和操作。

以下各部分详细介绍由受支持的事件生成的 Webhook 请求的架构。 事件部分包括事件类型的有效负载架构、示例请求有效负载以及触发 Webhook 的一个或多个示例命令。

有关为 Azure 容器注册表配置 Webhook 的信息，请参阅[使用 Azure 容器注册表 Webhook](container-registry-webhook.md)。

## <a name="webhook-requests"></a>Webhook 请求

### <a name="http-request"></a>HTTP 请求

配置 Webhook 时，触发的 Webhook 向指定的 URL 终结点发出 HTTP `POST` 请求。

### <a name="http-headers"></a>HTTP 标头

如果尚未指定 Webhook 的 `Content-Type` 自定义标头，则 Webhook 请求包括 `application/json` 的 `Content-Type`。

除为 Webhook 已指定的这些自定义标头外，不会向该请求添加任何其他标头。

## <a name="push-event"></a>Push 事件

容器映像推送到存储库时触发的 Webhook。

### <a name="push-event-payload"></a>Push 事件负载

|元素|Type|说明|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的 ID。|
|`timestamp`|DateTime|触发 Webhook 事件的时间。|
|`action`|String|触发 Webhook 事件的操作。|
|[目标](#target)|复杂类型|触发 Webhook 事件的事件目标。|
|[请求](#request)|复杂类型|生成 Webhook 事件的请求。|

### <a name="target"></a>目标

|元素|Type|说明|
|------------------|----------|-----------|
|`mediaType`|String|引用对象的 MIME 类型。|
|`size`|Int32|内容的字节数。 与 Length 字段相同。|
|`digest`|String|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`length`|Int32|内容的字节数。 与 Size 字段相同。|
|`repository`|String|存储库名称。|
|`tag`|String|映像标记名称。|

### <a name="request"></a>请求

|元素|Type|说明|
|------------------|----------|-----------|
|`id`|String|启动事件的请求 ID。|
|`host`|String|注册表实例的外部可访问主机名，由传入请求中的 HTTP 主机标头指定。|
|`method`|String|生成事件的请求方法。|
|`useragent`|String|请求的用户代理标头。|

### <a name="payload-example-push-event"></a>有效负载示例：push 事件

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

触发 push 事件 Webhook 的示例 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) 命令：

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>删除事件

删除存储库或清单时触发的 Webhook。 删除标记时不会触发 Webhook。

### <a name="delete-event-payload"></a>Delete 事件负载

|元素|Type|说明|
|-------------|----------|-----------|
|`id`|String|Webhook 事件的 ID。|
|`timestamp`|DateTime|触发 Webhook 事件的时间。|
|`action`|String|触发 Webhook 事件的操作。|
|[目标](#delete_target)|复杂类型|触发 Webhook 事件的事件目标。|
|[请求](#delete_request)|复杂类型|生成 Webhook 事件的请求。|

### <a name="delete_target"></a> 目标

|元素|Type|说明|
|------------------|----------|-----------|
|`mediaType`|String|引用对象的 MIME 类型。|
|`digest`|String|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`repository`|String|存储库名称。|

### <a name="delete_request"></a> 请求

|元素|Type|说明|
|------------------|----------|-----------|
|`id`|String|启动事件的请求 ID。|
|`host`|String|注册表实例的外部可访问主机名，由传入请求中的 HTTP 主机标头指定。|
|`method`|String|生成事件的请求方法。|
|`useragent`|String|请求的用户代理标头。|

### <a name="payload-example-delete-event"></a>有效负载示例：删除事件

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

触发 delete 事件 Webhook 的示例 [Azure CLI 2.0](/cli/azure/acr) 命令：

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>后续步骤

[使用 Azure 容器注册表 webhook](container-registry-webhook.md)