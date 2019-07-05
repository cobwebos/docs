---
title: 配置容器 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 了解如何将表单识别器容器配置为分析表单和表数据。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: e4b6a852ece3060ecf866d66d32f213355d99950
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592666"
---
# <a name="configure-form-recognizer-containers"></a>配置表单识别器容器

可以使用 Azure 表单识别器容器构建经过优化的应用程序体系结构，以利用强大的云功能和边缘位置。

可以使用 `docker run` 命令参数配置表单识别器容器运行时环境。 此容器有多个必需设置和一些可选设置。 有关示例，请查看[“Docker 运行命令示例”](#example-docker-run-commands)部分。 容器专用设置是帐单设置。

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。 必须为所有三项设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 ApiKey 的值必须是表单识别器资源的有效密钥，该资源是在“Billing 配置设置”部分为 `Billing` 指定的。 

可以在 Azure 门户的“表单识别器资源管理”的“密钥”下找到此设置。  

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器账单信息的表单识别器资源的终结点 URI。  此配置设置的值必须是 Azure 上表单识别器资源的有效终结点 URI。  容器约每 10 到 15 分钟报告一次使用情况。

可以在 Azure 门户的“表单识别器概览”的“终结点”下找到此设置。  

|需要| Name | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>示例：<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [`docker run` 命令](https://docs.docker.com/engine/reference/commandline/run/)中指定 `--mount` 选项来指定输入装载或输出装载。

表单识别器容器需要输入装载和输出装载。 输入装入点可以是只读的，需要使用它来访问用于训练和评分的数据。 输出装入点必须是可写的，用于存储模型和临时数据。

主机确切语法的安装位置因主机操作系统不同而异。 另外，由于 Docker 服务帐户权限与主机装载位置权限之间有冲突，因此可能无法访问[主计算机](form-recognizer-container-howto.md#the-host-computer)的装载位置。

|可选| 名称 | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|需要| `Input` | String | 输入装入点的目标。 默认值为 `/input`。    <br><br>示例：<br>`--mount type=bind,src=c:\input,target=/input`|
|需要| `Output` | String | 输出装入点的目标。 默认值为 `/output`。  <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。 运行后，容器将继续运行，直到[停止它](form-recognizer-container-howto.md#stop-the-container)。

* **行继续符**：以下各部分中的 Docker 命令使用反斜杠 (\\) 作为行继续符。 根据主机操作系统的要求替换或删除此字符。
* **参数顺序**：除非熟悉 Docker 容器，否则不要更改参数顺序。

将下表中的 {_argument_name_} 替换为为你自己的值：

| 占位符 | 值 |
|-------------|-------|
|{BILLING_KEY} | 用于启动容器的密钥。 可以从 Azure 门户的“表单识别器密钥”页获取它。  |
|{BILLING_ENDPOINT_URI} | 可以从 Azure 门户的“表单识别器概览”页获取计费终结点 URI 值。|
|{COMPUTER_VISION_API_KEY}| 可以从 Azure 门户的“计算机视觉 API 密钥”页获取此密钥。|
|{COMPUTER_VISION_ENDPOINT_URI}|计费终结点。 如果使用基于云的计算机视觉资源，则可以从 Azure 门户的“计算机视觉 API 概览”页获取 URI 值。 如果使用 *cognitive-services-recognize-text* 容器，请使用在 `docker run` 命令中传递给容器的计费终结点 URL。|

> [!IMPORTANT]
> 若要运行该容器，请指定 `Eula`、`Billing` 和 `ApiKey` 选项；否则，该容器不会启动。 有关详细信息，请参阅[计费](#billing-configuration-setting)。

> [!NOTE] 
> ApiKey 值是 Azure 表单识别器“资源密钥”页中提供的“密钥”  。

## <a name="form-recognizer-container-docker-examples"></a>表单识别器容器 Docker 示例

下面是表单识别器容器的 Docker 示例。

### <a name="basic-example-for-form-recognizer"></a>表单识别器基本示例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>表单识别器日志记录示例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>后续步骤

* 查看[安装和运行容器](form-recognizer-container-howto.md)。
