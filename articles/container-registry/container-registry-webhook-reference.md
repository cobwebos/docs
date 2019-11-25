---
title: Registry webhook schema reference
description: Reference for JSON payload for webhook requests in an Azure container registry, which are generated when webhooks are enabled for artifact push or delete events
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455974"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure 容器注册表 Webhook 参考

可以为容器注册表[配置 Webhook](container-registry-webhook.md)，以便对其执行某些操作时生成相应事件。 For example, enable webhooks that are triggered when a container image or Helm chart is pushed to a registry, or deleted. 触发 Webhook 后，Azure 容器注册表向指定的终结点发出 HTTP 或 HTTPS 请求并包含有关此事件的信息。 然后终结点处理相应的 Webhook 和操作。

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

|元素|Type|描述|
|-------------|----------|-----------|
|`id`|字符串|Webhook 事件的 ID。|
|`timestamp`|日期/时间|触发 Webhook 事件的时间。|
|`action`|字符串|触发 Webhook 事件的操作。|
|[目标](#target)|复杂类型|触发 Webhook 事件的事件目标。|
|[请求](#request)|复杂类型|生成 Webhook 事件的请求。|

### <a name="target"></a>target

|元素|Type|描述|
|------------------|----------|-----------|
|`mediaType`|字符串|引用对象的 MIME 类型。|
|`size`|Int32|内容的字节数。 与 Length 字段相同。|
|`digest`|字符串|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`length`|Int32|内容的字节数。 与 Size 字段相同。|
|`repository`|字符串|存储库名称。|
|`tag`|字符串|映像标记名称。|

### <a name="request"></a>request

|元素|Type|描述|
|------------------|----------|-----------|
|`id`|字符串|启动事件的请求 ID。|
|`host`|字符串|注册表实例的外部可访问主机名，由传入请求中的 HTTP 主机标头指定。|
|`method`|字符串|生成事件的请求方法。|
|`useragent`|字符串|请求的用户代理标头。|

### <a name="payload-example-image-push-event"></a>Payload example: image push event

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Example [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) command that triggers the image **push** event webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Chart push event

Webhook triggered when a Helm chart is pushed to a repository.

### <a name="chart-push-event-payload"></a>Chart push event payload

|元素|Type|描述|
|-------------|----------|-----------|
|`id`|字符串|Webhook 事件的 ID。|
|`timestamp`|日期/时间|触发 Webhook 事件的时间。|
|`action`|字符串|触发 Webhook 事件的操作。|
|[目标](#helm_target)|复杂类型|触发 Webhook 事件的事件目标。|

### <a name="helm_target"></a>target

|元素|Type|描述|
|------------------|----------|-----------|
|`mediaType`|字符串|引用对象的 MIME 类型。|
|`size`|Int32|内容的字节数。|
|`digest`|字符串|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`repository`|字符串|存储库名称。|
|`tag`|字符串|The chart tag name.|
|`name`|字符串|The chart name.|
|`version`|字符串|The chart version.|

### <a name="payload-example-chart-push-event"></a>Payload example: chart push event

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Example [Azure CLI](/cli/azure/acr) command that triggers the **chart_push** event webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>删除活动

Webhook triggered when an image repository or manifest is deleted. 删除标记时不会触发 Webhook。

### <a name="delete-event-payload"></a>Delete 事件负载

|元素|Type|描述|
|-------------|----------|-----------|
|`id`|字符串|Webhook 事件的 ID。|
|`timestamp`|日期/时间|触发 Webhook 事件的时间。|
|`action`|字符串|触发 Webhook 事件的操作。|
|[目标](#delete_target)|复杂类型|触发 Webhook 事件的事件目标。|
|[请求](#delete_request)|复杂类型|生成 Webhook 事件的请求。|

### <a name="delete_target"></a> 目标

|元素|Type|描述|
|------------------|----------|-----------|
|`mediaType`|字符串|引用对象的 MIME 类型。|
|`digest`|字符串|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`repository`|字符串|存储库名称。|

### <a name="delete_request"></a> 请求

|元素|Type|描述|
|------------------|----------|-----------|
|`id`|字符串|启动事件的请求 ID。|
|`host`|字符串|注册表实例的外部可访问主机名，由传入请求中的 HTTP 主机标头指定。|
|`method`|字符串|生成事件的请求方法。|
|`useragent`|字符串|请求的用户代理标头。|

### <a name="payload-example-image-delete-event"></a>Payload example: image delete event

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

触发**删除**事件 Webhook 的示例 [Azure CLI](/cli/azure/acr) 命令：

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Chart delete event

Webhook triggered when a Helm chart or repository is deleted. 

### <a name="chart-delete-event-payload"></a>Chart delete event payload

|元素|Type|描述|
|-------------|----------|-----------|
|`id`|字符串|Webhook 事件的 ID。|
|`timestamp`|日期/时间|触发 Webhook 事件的时间。|
|`action`|字符串|触发 Webhook 事件的操作。|
|[目标](#chart_delete_target)|复杂类型|触发 Webhook 事件的事件目标。|

### <a name="chart_delete_target"></a> 目标

|元素|Type|描述|
|------------------|----------|-----------|
|`mediaType`|字符串|引用对象的 MIME 类型。|
|`size`|Int32|内容的字节数。|
|`digest`|字符串|内容摘要，由注册表 V2 HTTP API 规范定义。|
|`repository`|字符串|存储库名称。|
|`tag`|字符串|The chart tag name.|
|`name`|字符串|The chart name.|
|`version`|字符串|The chart version.|

### <a name="payload-example-chart-delete-event"></a>Payload example: chart delete event

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Example [Azure CLI](/cli/azure/acr) command that triggers the **chart_delete** event webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>后续步骤

[使用 Azure 容器注册表 webhook](container-registry-webhook.md)