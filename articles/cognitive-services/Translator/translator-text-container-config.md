---
title: 配置容器-文本翻译
titleSuffix: Azure Cognitive Services
description: 文本翻译提供包含通用配置框架的容器，因此你可以轻松地配置和管理容器的存储、日志记录、遥测和安全设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507667"
---
# <a name="configure-translator-text-docker-containers"></a>配置文本翻译 docker 容器

文本翻译为每个容器提供一个通用的配置框架，以便你可以轻松地配置和管理容器的存储、日志记录和遥测以及安全设置。

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|需要|设置|目的|
|--|--|--|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可证。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|HTTP 代理|配置 HTTP 代理以发出出站请求。|
|否|[Logging](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|否|[Mounts](#mount-settings)|从主计算机读取数据并将其写入到容器，以及从容器读回数据并将其写回到主计算机。|

> [!IMPORTANT]
> 必须为[`Eula`](#eula-setting)设置提供 `accept`的值;否则，容器将无法启动。

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 `--mount`docker run[ 命令中指定 ](https://docs.docker.com/engine/reference/commandline/run/) 选项来指定输入装载或输出装载。

文本翻译容器不使用输入或输出装入来存储定型或服务数据。 

主机装载位置的确切语法不同，具体取决于主机操作系统。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](how-to-install-containers.md#the-host-computer)的装载位置。 

|可选| 名称 | 数据类型 | 说明 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 文本翻译容器不使用此。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](how-to-install-containers.md#stop-the-container)它。

* **行继续**符：以下部分中的 docker 命令使用反斜杠（`\`）作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：不要更改参数的顺序，除非你非常熟悉 docker 容器。

## <a name="translator-text-container-docker-examples"></a>文本翻译容器 docker 示例

以下 docker 示例适用于文本翻译容器。

### <a name="basic-example"></a>基本示例

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>日志记录示例

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](how-to-install-containers.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
