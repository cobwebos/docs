---
title: Docker 容器-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 容器可将已训练或已发布的应用加载到 docker 容器中并提供对容器的 API 终结点中的查询预测的访问权限。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390153"
---
# <a name="install-and-run-luis-docker-containers"></a>安装并运行 LUIS docker 容器
 
语言理解（LUIS）容器语言理解模型中加载已定型或已发布的模型。 作为[LUIS 应用](https://www.luis.ai)，docker 容器提供对容器的 API 终结点中查询预测的访问。 可从容器中收集查询日志，并将其上传回语言理解的应用，以提高应用的预测准确性。

以下视频演示如何使用此容器。

[![认知服务的容器演示](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>系统必备

若要运行 LUIS 容器，请注意以下先决条件：

|必需|用途|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|Azure `Cognitive Services` 资源和 LUIS[打包的应用](luis-how-to-start-new-app.md)文件 |若要使用容器，必须具有：<br><br>*_认知服务_Azure 资源和关联的计费密钥的计费终结点 URI。 这两个值都可用于资源的 "概述" 和 "键" 页，并且是启动容器所必需的。 <br>* 已训练或已发布的应用，作为已安装的输入打包到具有其关联的应用 ID 的容器。 可以从 LUIS 门户或创作 Api 获取打包的文件。 如果从[创作 api](#authoring-apis-for-package-file)获取 LUIS 打包应用，还需要_创作密钥_。<br><br>这些要求用于将命令行参数传递到以下变量：<br><br>**{AUTHORING_KEY}** ：此密钥用于从云中的 LUIS 服务获取打包的应用并将查询日志上传回云。 格式为 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。<br><br>**{APP_ID}** ：此 ID 用于选择应用。 格式为 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。<br><br>**{API_KEY}** ：此密钥用于启动容器。 可以在两个位置找到终结点密钥。 第一个是_认知服务_资源的密钥列表中的 Azure 门户。 也可以在 LUIS 门户的“密钥和终结点”设置页上找到终结点密钥。 请勿使用初学者密钥。<br><br>**{ENDPOINT_URI}** ："概述" 页中提供的终结点。<br><br>[创作密钥和终结点密钥](luis-boundaries.md#key-limits)具有不同的用途。 请勿互换使用。 |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>为包文件创作 Api

创作适用于打包应用的 Api：

* [已发布包 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [未发布，只经过训练的包 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

此容器支持设置的最小值和建议值：

|容器| 最低要求 | 建议 | TPS<br>（最小值、最大值）|
|-----------|---------|-------------|--|
|LUIS|单核，2 GB 内存|1核，4 GB 内存|20，40|

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TPS - 每秒事务数

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 `mcr.microsoft.com/azure-cognitive-services/luis` 存储库下载容器映像：

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

有关可用标记的完整说明（如上述命令中使用的 `latest`），请参阅 Docker Hub 上的 [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204)。

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

![使用语言理解 (LUIS) 容器的过程](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. 通过 LUIS 门户或 LUIS API [导出容器的包](#export-packaged-app-from-luis)。
1. 将包文件移动到[主计算机](#the-host-computer)上的所需输入目录中。 请勿重命名、更改、覆盖或解压缩 LUIS 包文件。
1. 使用所需的输入装入点和计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](luis-container-configuration.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 
1. 使用完此容器后，从 LUIS 门户的输出装入点[导入终结点日志](#import-the-endpoint-logs-for-active-learning)并[停止](#stop-the-container)容器。
1. 在“查看终结点话语”页上使用 LUIS 门户的[主动学习](luis-how-to-review-endpoint-utterances.md)改进应用。

无法更改正在容器中运行的应用。 若要更改容器中的应用，必须使用 [LUIS](https://www.luis.ai) 门户或使用 LUIS [创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) 更改 LUIS 服务中的应用。 然后进行训练和/或发布，下载新包并再次运行该容器。

容器内的 LUIS 应用无法导出回 LUIS 服务。 只能上传查询日志。 

## <a name="export-packaged-app-from-luis"></a>从 LUIS 导出打包的应用

LUIS 容器需要已训练或已发布的 LUIS 应用才能回复用户话语的预测查询。 若要获取 LUIS 应用，请使用已训练或已发布的包 API。 

默认位置是运行 `docker run` 命令的 `input` 子目录。  

将包文件置于某个目录中，然后在运行 docker 容器时引用此目录作为输入装入点。 

### <a name="package-types"></a>包类型

输入装入目录可以同时包含应用的**生产**、**暂存**和**版本控制**模型。 所有包均已装载。

|包类型|查询终结点 API|查询可用性|包文件名格式|
|--|--|--|--|
|版本|GET、POST|仅容器|`{APP_ID}_v{APP_VERSION}.gz`|
|过渡|GET、POST|Azure 和容器|`{APP_ID}_STAGING.gz`|
|生产|GET、POST|Azure 和容器|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> 请勿重命名、更改、覆盖或解压缩 LUIS 包文件。

### <a name="packaging-prerequisites"></a>打包先决条件

在打包 LUIS 应用程序之前，必须满足以下条件：

|打包要求|详细信息|
|--|--|
|Azure_认知服务_资源实例|支持的区域包括<br><br>美国西部 (`westus`)<br>西欧 (`westeurope`)<br>澳大利亚东部 (`australiaeast`)|
|已训练或已发布的 LUIS 应用|没有[不受支持的依赖项][unsupported-dependencies]。 |
|访问[主计算机](#the-host-computer)的文件系统 |主计算机必须允许[输入装入点](luis-container-configuration.md#mount-settings)。|
  
### <a name="export-app-package-from-luis-portal"></a>通过 LUIS 门户导出应用包

LUIS [门户](https://www.luis.ai)提供导出已训练或已发布的应用包的功能。

### <a name="export-published-apps-package-from-luis-portal"></a>通过 LUIS 门户导出已发布的应用包

可从“我的应用”列表页中获取已发布的应用包。 

1. 登录到 LUIS [门户](https://www.luis.ai)。
1. 选中列表中应用名称左侧的复选框。 
1. 从列表上方的上下文工具栏中选择“导出”项。
1. 选择“导出容器 (GZIP)”。
1. 选择“生产槽”或“过渡槽”的环境。
1. 将从浏览器下载包。

![从“应用”页面的“导出”菜单导出容器的已发布包](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>从 LUIS 门户导出已版本控制的应用包

**版本**列表页提供了版本控制的应用包。

1. 登录到 LUIS [门户](https://www.luis.ai)。
1. 在列表中选择应用。 
1. 在应用的导航栏中选择“管理”。
1. 在左侧导航栏中选择“版本”。
1. 选中列表中版本名称左侧的复选框。
1. 从列表上方的上下文工具栏中选择“导出”项。
1. 选择“导出容器 (GZIP)”。
1. 将从浏览器下载包。

![从“版本”页面的“导出”菜单导出容器的已训练包](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>通过 API 导出已发布的应用包

使用以下 REST API 方法打包[已发布](luis-how-to-publish-app.md)的 LUIS 应用。 使用 HTTP 规范下方的表将 API 调用中的占位符替换为自己相应的值。

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 占位符 | 值 |
|-------------|-------|
| **{APP_ID}** | 已发布 LUIS 应用的应用程序 ID。 |
| **{SLOT_NAME}** | 已发布 LUIS 应用的环境。 使用以下值之一：<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | 已发布 LUIS 应用的 LUIS 帐户的创作密钥。<br/>可以从 LUIS 门户的“用户设置”页面中获取创作密钥。 |
| **{AZURE_REGION}** | 相应的 Azure 区域：<br/><br/>`westus` - 美国西部<br/>`westeurope` - 西欧<br/>`australiaeast` - 澳大利亚东部 |

若要下载已发布的包，请参阅[此处的 API 文档][download-published-package]。 如果成功下载，响应为 LUIS 包文件。 将文件保存在为容器的输入装入点指定的存储位置中。 

### <a name="export-versioned-apps-package-from-api"></a>从 API 导出已版本控制的应用包

使用以下 REST API 方法打包[已训练](luis-how-to-train.md)的 LUIS 应用程序。 使用 HTTP 规范下方的表将 API 调用中的占位符替换为自己相应的值。

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 占位符 | 值 |
|-------------|-------|
| **{APP_ID}** | 训练的 LUIS 应用的应用程序 ID。 |
| **{APP_VERSION}** | 训练的 LUIS 应用的应用程序版本。 |
| **{AUTHORING_KEY}** | 已发布 LUIS 应用的 LUIS 帐户的创作密钥。<br/>可以从 LUIS 门户的“用户设置”页面中获取创作密钥。 |
| **{AZURE_REGION}** | 相应的 Azure 区域：<br/><br/>`westus` - 美国西部<br/>`westeurope` - 西欧<br/>`australiaeast` - 澳大利亚东部 |

若要下载版本控制的包，请参阅[此处的 API 文档][download-versioned-package]。 如果成功下载，响应为 LUIS 包文件。 将文件保存在为容器的输入装入点指定的存储位置中。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集必需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](luis-container-configuration.md#example-docker-run-commands)可用。

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* 此示例使用 `C:` 驱动器上的目录，以避免在 Windows 上出现任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。 
* 除非你熟悉 docker 容器，否则不要更改参数的顺序。
* 如果你使用的是不同的操作系统，请使用正确的控制台/终端、用于装载的文件夹语法以及系统的行继续符。 这些示例假定 Windows 控制台的行继续符 `^`。 由于容器是 Linux 操作系统，因此目标装载使用 Linux 样式的文件夹语法。

此命令：

* 从 LUIS 容器映像运行容器
* 从位于容器主机上的*C:\input*上的输入装载加载 LUIS 应用
* 分配两个 CPU 内核和 4 千兆字节 (GB) 的内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 将容器和 LUIS 日志保存到位于容器主机上的*C:\output*的输出装入
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](luis-container-configuration.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。
> ApiKey 值是 LUIS 门户的 " **Azure 资源**" 页中的**密钥**，也可在 "azure `Cognitive Services` 资源密钥" 页中找到。  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>容器支持的终结点 Api

容器的 V2 和[V3](luis-migration-api-v3.md)版本均可用于容器。 

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 已发布（过渡或生产）应用的终结点的路由_不同_于版本控制的应用的终结点。

使用主机 `http://localhost:5000`，以获得容器 API。

# <a name="v3-prediction-endpoint"></a>[V3 预测终结点](#tab/v3)

|包类型|HTTP 谓词|路由|查询参数|
|--|--|--|--|
|已发布|GET、POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|版本|GET、POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

查询参数配置查询响应的返回方式以及返回内容：

|查询参数|Type|用途|
|--|--|--|
|`query`|字符串|用户的话语。|
|`verbose`|boolean|一个布尔值，指示是否返回预测模型的所有元数据。 默认值为 false。|
|`log`|boolean|记录查询，以便以后用于[主动学习](luis-how-to-review-endpoint-utterances.md)。 默认值为 false。|
|`show-all-intents`|boolean|一个布尔值，该值指示是只返回所有意向还是仅返回 top 计分意向。 默认值为 false。|

# <a name="v2-prediction-endpoint"></a>[V2 预测终结点](#tab/v2)

|包类型|HTTP 谓词|路由|查询参数|
|--|--|--|--|
|已发布|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)、 [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|版本|GET、POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

查询参数配置查询响应的返回方式以及返回内容：

|查询参数|Type|用途|
|--|--|--|
|`q`|字符串|用户的话语。|
|`timezoneOffset`|number|通过 timezoneOffset 可以[更改时区](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)，该时区由预生成实体 datetimeV2 使用。|
|`verbose`|boolean|设置为 true 时，返回所有意向及其分数。 默认值为 false 时，仅返回评分最高的意向。|
|`staging`|boolean|设置为 true 时，返回过渡环境结果中的查询。 |
|`log`|boolean|记录查询，以便以后用于[主动学习](luis-how-to-review-endpoint-utterances.md)。 默认值为 true。|

***

### <a name="query-the-luis-app"></a>查询 LUIS 应用

用于查询已发布应用的容器的示例 CURL 命令是：

# <a name="v3-prediction-endpoint"></a>[V3 预测终结点](#tab/v3)

若要查询槽中的模型，请使用以下 API：

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

若要对**过渡**环境进行查询，请将路由中的 `production` 替换为 `staging`：

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

若要查询版本控制的模型，请使用以下 API：

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 预测终结点](#tab/v2)

若要查询槽中的模型，请使用以下 API：

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
若要对过渡环境执行查询，请将过渡查询字符串参数值更改为 true： 

`staging=true`

若要查询版本控制的模型，请使用以下 API：

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
版本名称最多包含 10 个字符，并且仅包含 URL 中允许的字符。

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>导入终结点日志以供主动学习

如果为 LUIS 容器指定了输出装载，应用查询日志文件将保存在输出目录中，其中 `{INSTANCE_ID}` 是容器 ID。 应用查询日志包含提交到 LUIS 容器的每个预测查询的查询、响应和时间戳。 

以下位置显示了容器的日志文件的嵌套目录结构。
```
/output/luis/{INSTANCE_ID}/
```
 
从 LUIS 门户中选择应用，然后选择“导入终结点日志”以上传这些日志。 

![导入容器的日志文件以供主动学习](./media/luis-container-how-to/upload-endpoint-log-files.png)

上传日志后，在 LUIS 门户中[查看终结点](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)话语。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

若要关闭容器，请在运行容器的命令行环境中按 Ctrl+C。

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](luis-container-configuration.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>账单

LUIS 容器使用 Azure 帐户上的_认知服务_资源将计费信息发送到 azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](luis-container-configuration.md)。

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

在本文中，我们已学习相关的概念，以及语言理解 (LUIS) 容器的下载、安装和运行工作流。 综上所述：

* 语言理解 (LUIS) 为 Docker 提供一个 Linux 容器，用于提供话语的终结点查询预测。
* 从 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 来通过指定容器的主机 URI 查询容器终结点。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](luis-container-configuration.md)，了解配置设置。
* 请参阅[LUIS 容器](luis-container-limitations.md)对已知功能限制的限制。
* 若要解决与 LUIS 功能相关的问题，请参阅[故障排除](troubleshooting.md)。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
