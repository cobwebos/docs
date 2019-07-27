---
title: 配置容器-人脸 API
titleSuffix: Azure Cognitive Services
description: 容器配置设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 6dd047b0ba7f9a123ffcc014cff5604466946d07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564098"
---
# <a name="configure-face-docker-containers"></a>配置人脸 Docker 容器

使用 `docker run` 命令参数配置人脸容器运行时环境。 此容器有多个必需设置，以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 容器专用设置是帐单设置。 

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](face-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，且此值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的“认知服务”资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**“资源管理”部分的“密钥”下

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器帐单信息的“认知服务”资源的终结点 URI。 必须为这个配置设置指定值，且此值必须是 Azure 上“认知服务”资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**概述，标记为 `Endpoint`

请记得将人_脸_路由添加到终结点 URI, 如示例中所示。 

|必填| 名称 | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>例如：<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI 配置设置

`CloudAI` 部分中的配置设置提供容器特有的容器特定选项。 `CloudAI` 部分中的人脸容器支持以下设置和对象

| 名称 | 数据类型 | 描述 |
|------|-----------|-------------|
| `Storage` | Object | 人脸容器使用的存储方案。 有关 `Storage` 对象的存储方案和关联设置的详细信息，请参阅[存储方案设置](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>存储方案设置

人脸容器存储 blob、缓存、元数据和队列数据，具体取决于存储的内容。 例如，大型用户组的培训索引和结果存储为 blob 数据。 人脸容器在与这些类型的数据交互以及存储这些类型的数据时会提供两种不同的存储方案：

* 内存  
  所有四种类型的数据都存储在内存中。 它们不是分布式的，也不是持久性的。 如果停止或删除人脸容器，则会销毁该容器的所有存储数据。  
  这是人脸容器的默认存储方案。
* Azure  
  人脸容器使用 Azure 存储和 Azure Cosmos DB 在持久存储中分发这四种类型的数据。 Azure 存储处理 blob 和队列数据。 由 Azure Cosmos DB 处理元数据和缓存数据。 如果停止或删除人脸容器，则该容器的所有存储数据仍保存在 Azure 存储和 Azure Cosmos DB 中。  
  Azure 存储方案使用的资源具有以下附加要求
  * Azure 存储资源必须使用 StorageV2 帐户类型
  * Azure Cosmos DB 资源必须使用用于 MongoDB 的 Auzre Cosmos DB API

存储方案和关联的配置设置由 `Storage` 对象在 `CloudAI` 配置部分下进行管理。 `Storage` 对象中提供了以下配置设置：

| 名称 | 数据类型 | 描述 |
|------|-----------|-------------|
| `StorageScenario` | String | 容器支持的存储方案。 可用值如下<br/>`Memory` - 默认值。 容器使用非持久、非分布式的内存中存储，用于单节点的临时使用情况。 如果停止或删除容器，则该容器的存储将被销毁。<br/>`Azure` - 容器使用 Azure 资源进行存储。 如果停止或删除容器，则会保留该容器的存储。|
| `ConnectionStringOfAzureStorage` | String | 容器使用的 Azure 存储资源的连接字符串。<br/>仅当为 `StorageScenario` 配置设置指定了 `Azure` 时，才应用此设置。 |
| `ConnectionStringOfCosmosMongo` | String | 容器使用的 Azure Cosmos DB 资源的 MongoDB 连接字符串。<br/>仅当为 `StorageScenario` 配置设置指定了 `Azure` 时，才应用此设置。 |

例如，以下命令指定 Azure 存储方案，并为用于存储人脸容器数据的 Azure 存储和 Cosmos DB 资源提供示例连接字符串。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

存储方案的处理是与输入装入点和输出装入点的处理分开的。 可以为单个容器指定这些功能的组合。 例如，以下命令在主机上的 `D:\Output` 文件夹中定义一个 Docker 绑定装入点作为输出装入点，然后从人脸容器映像实例化容器，并将 JSON 格式的日志文件保存到输出装入点。 以下命令还指定 Azure 存储方案，并为用于存储人脸容器数据的 Azure 存储和 Cosmos DB 资源提供示例连接字符串。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。

人脸容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 另外，由于 Docker 服务帐户使用的权限与主机装载位置权限之间有冲突，因此可能无法访问[主计算机](face-how-to-install-containers.md#the-host-computer)的装载位置。 

|可选| 名称 | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 人脸容器不使用此项。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>例如：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](face-how-to-install-containers.md#stop-the-container)它。

* **行继续符**：以下各部分中的 Docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非很熟悉 Docker 容器，否则不要更改参数顺序。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | ReplTest1 | 格式或示例 |
|-------------|-------|---|
|{API_KEY} | 认知服务资源的终结点密钥。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | 终结点值, 包括区域和人脸路由。|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](face-how-to-install-containers.md#billing)。
> ApiKey 值是来自 Azure `Cognitive Services`“资源密钥”页的“密钥”。 

## <a name="face-container-docker-examples"></a>人脸容器 Docker 示例

下面是人脸容器 Docker 示例。 

### <a name="basic-example"></a>基本示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](face-how-to-install-containers.md)
