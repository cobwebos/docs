---
title: 配置语音容器
titleSuffix: Azure Cognitive Services
description: 语音服务为每个容器提供通用的配置框架，以便你可以轻松地配置和管理容器的存储、日志记录和遥测以及安全设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 4e09a476398134d92b4492c68ed4ebebc468f272
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796180"
---
# <a name="configure-speech-service-containers"></a>配置语音服务容器

语音容器使客户能够构建一个经过优化的语音应用程序体系结构，以利用强大的云功能和边缘位置。 现在，我们支持的四个语音容器为：**语音到文本**、**自定义语音到文本**、**文本到语音**转换和**自定义文本到语音转换**。

**语音**容器运行时环境使用 `docker run` 命令参数进行配置。 此容器有多个必需设置，以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 容器专用设置是帐单设置。 

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](speech-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，并且该值必须是为[ 配置设置指定的语音`Billing`](#billing-configuration-setting)资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**语音的**资源管理，在 "**密钥**" 下

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器的账单信息的语音资源的终结点 URI。 必须为此配置设置指定值，并且该值必须是 Azure 上语音资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**语音**概述，标记 `Endpoint`

|必选| 名称 | 数据类型 | 说明 |
|--|------|-----------|-------------|
|是| `Billing` | String | 计费终结点 URI。 有关获取计费 URI 的详细信息，请参阅[收集所需的参数](speech-container-howto.md#gathering-required-parameters)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 `--mount`docker run[ 命令中指定 ](https://docs.docker.com/engine/reference/commandline/run/) 选项来指定输入装载或输出装载。

标准语音容器不使用输入或输出装入来存储定型或服务数据。 但是，自定义语音容器依赖于卷装入。

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](speech-container-howto.md#the-host-computer)的装载位置。 

|可选| 名称 | 数据类型 | 说明 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 标准语音容器不使用此功能。 自定义语音容器使用[卷装入](#volume-mount-settings)。 |
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="volume-mount-settings"></a>卷装入设置

自定义语音容器使用[卷装入](https://docs.docker.com/storage/volumes/)来持久保存自定义模型。 可以通过将 `-v` （或 `--volume`）选项添加到[docker run](https://docs.docker.com/engine/reference/commandline/run/)命令来指定卷装入。

首次将新模型引入为自定义语音容器 docker run 命令的一部分时，将下载自定义模型。 自定义语音容器的顺序运行相同的 `ModelId` 将使用先前下载的模型。 如果未提供卷装入，则不能持久保存自定义模型。

卷装入设置由三个颜色 `:` 分隔字段组成：

1. 第一个字段是主计算机上的卷的名称，例如*C:\input*。
2. 第二个字段是容器中的目录，例如 */usr/local/models*。
3. 第三个字段（可选）是以逗号分隔的选项列表。有关详细信息，请参阅[使用卷](https://docs.docker.com/storage/volumes/)。

### <a name="volume-mount-example"></a>卷装入示例

```bash
-v C:\input:/usr/local/models
```

此命令将主机*C:\input*目录装载到容器 */usr/local/models*目录中。

> [!IMPORTANT]
> 卷装入设置仅适用于**自定义语音到文本**的和**自定义的文本到语音转换**容器。 标准的**语音到文本**和**文本到语音**的容器不使用卷装入。

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](speech-container-howto.md#stop-the-container)它。

* **行继续**符：以下部分中的 Docker 命令使用反斜杠（`\`）作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非你熟悉 Docker 容器，否则不要更改参数的顺序。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | 值 | 格式或示例 |
|-------------|-------|---|
| **{API_KEY}** | “Azure `Speech` 密钥”页上的 `Speech` 资源的终结点密钥。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Azure `Speech`“概览”页面上提供了账单终结点值。| 有关显式示例，请参阅[收集所需的参数](speech-container-howto.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing-configuration-setting)。
> ApiKey 值是来自“ Azure 语音资源密钥”页的“密钥”。 

## <a name="speech-container-docker-examples"></a>语音容器 Docker 示例

以下 Docker 示例适用于语音容器。 

# <a name="speech-to-texttabstt"></a>[语音转文本](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>语音到文本的基本示例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>语音到文本的日志记录示例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-speech-to-texttabcstt"></a>[自定义语音到文本](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>自定义语音到文本的基本示例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>自定义语音到文本的日志记录示例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="text-to-speechtabtss"></a>[文本转语音](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>文本到语音转换的基本示例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>文本到语音转换的日志记录示例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-text-to-speechtabctts"></a>[自定义文本到语音转换](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>自定义文本到语音转换的基本示例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>自定义文本到语音功能的日志记录示例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

***

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](speech-container-howto.md)
