---
title: 安装和运行容器-文本翻译
titleSuffix: Azure Cognitive Services
description: 本演练教程介绍如何下载、安装和运行文本翻译 Analytics 的容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507876"
---
# <a name="install-and-run-translator-text-containers"></a>安装并运行文本翻译容器

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

容器使你能够在自己的环境中运行文本翻译 Api，这非常适合于特定的安全和数据管理要求。

## <a name="prerequisites"></a>必备组件

使用文本翻译容器之前，必须满足以下先决条件：

|需要|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。|

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

必须先完成并提交[认知文本翻译容器请求窗体](https://aka.ms/translatorcontainerform)，才能请求访问容器。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>主计算机

宿主是一个基于 x64 的计算机，其中包含运行 Docker 容器的 Linux 操作系统。 它可以是本地计算机或 Azure 中的 Docker 托管服务，例如：

* Azure Kubernetes 服务。
* Azure 容器实例。
* 部署到 Azure Stack 的[Kubernetes](https://kubernetes.io/)群集。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了为每个文本翻译容器分配的最低和推荐的 CPU 内核数，至少为2.6 千兆赫（GHz）或更快，内存以千兆字节（GB）为单位。

| 容器 | 最小值 | 语言对 |
|-----------|---------|---------------|
| 文本翻译 | 4核，4 GB 内存 | 4 |

对于每个语言对，建议使用 1 GB 的内存。 默认情况下，根据正在运行的 `<image-tag>`，文本翻译容器具有3或4个语言对。 有关详细信息，请参阅[支持的语言和翻译](#supported-languages-and-translation)。 核心和内存对应于 `--cpus` 和 `--memory` 设置，这些设置将用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

以下容器存储库中提供了文本翻译的容器映像。 该表还会映射容器映像标记及其对应的支持语言。

| 容器 | 图像标记 | 支持的语言 |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`、`zh-CN`、`de-DE` 和 `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`、`fr-FR` 和 `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>针对文本翻译容器的 Docker 请求

若要执行[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令，首先需要访问容器注册表。 可以从 Azure CLI 使用[`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login)命令登录到 Azure 容器注册表。

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

此命令将通过相应的 Azure 容器注册表对当前用户进行身份验证。 现在，可以随意执行 `docker pull` 命令。

> [!NOTE]
> 根据所需的语言支持，请提供相应的 `<image-tag>`。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. [运行容器](#run-the-container-with-docker-run)。 提供 [ 命令的多个](translator-text-container-config.md#example-docker-run-commands)示例`docker run`。
1. [查询容器的转换终结点](#query-the-containers-translate-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行三个容器中的任意一个。 [ 命令的](translator-text-container-config.md#example-docker-run-commands)示例`docker run`可用。

### <a name="run-container-example-of-docker-run-command"></a>Docker run 命令的运行容器示例

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

此命令：

* 从容器映像运行文本翻译容器
* 分配4个 CPU 核心和4千兆字节（GB）的内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 接受最终用户协议（EULA）
* 退出后自动删除容器。 容器映像在主计算机上仍可用

### <a name="how-to-collect-docker-logs"></a>如何收集 docker 日志

出于故障排除目的，查看容器执行的 docker 日志可能会很有用。 首先，执行具有格式标志的[docker ps](https://docs.docker.com/engine/reference/commandline/ps/)命令，以限制为所有容器显示的详细信息。

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

然后，运行[docker logs](https://docs.docker.com/engine/reference/commandline/logs/)命令，其中包含相关容器的 `<Container ID>`，以便查看其日志。

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

上面的 docker 日志命令将收集过去四小时的 "错误" 日志。

## <a name="supported-languages-and-translation"></a>支持的语言和翻译

`POST /translate` 方法支持以下语言转换，从*英语*移动到目标语言，反之亦然。 请注意，虽然你可以在其中一种语言的情况下和从英语开始，但你*无法*从一种*非英语*语言转为另一种*非英语*语言。

> [!NOTE]
> 为了获得最佳质量，使用者只应为每个请求发送一个句子。

| 语言转换 | 语言 ISO 转换 | 图像标记 |
|--|--|:--|
| 英语： left_right_arrow：中文 | `en-US`： left_right_arrow： `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| 英语： left_right_arrow：俄语 | `en-US`： left_right_arrow： `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| 英语： left_right_arrow：阿拉伯语 | `en-US`： left_right_arrow： `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| 英语： left_right_arrow：德语 | `en-US`： left_right_arrow： `de-DE` | `ar_de_en_ru_zh_1.0.0` 和 `de_en_es_fr_1.0.0` |
| 英语： left_right_arrow：西班牙语 | `en-US`： left_right_arrow： `es-ES` | `de_en_es_fr_1.0.0` |
| 英语： left_right_arrow：法语 | `en-US`： left_right_arrow： `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> 若要运行容器，必须指定 `Eula`;否则，容器将无法启动。

## <a name="query-the-containers-translate-endpoint"></a>查询容器的转换终结点

容器提供基于 REST 的转换终结点 API。 此终结点的几个示例用法如下所示：

# <a name="curltabcurl"></a>[cURL](#tab/curl)


从所需的 CLI 执行以下卷曲命令。

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> 如果在容器准备就绪之前尝试此卷 POST，最终会获得 "服务暂时不可用" 响应。 只需等待容器准备就绪，然后重试。

以下输出将打印到控制台。

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

导航到 swagger 页面， http://localhost:5000/swagger/index.html

1. 选择**POST/translate**
1. 选择**试用**
1. 输入**From**参数作为 `en-US`
1. 输入**To**参数作为 `de-DE`
1. 输入**api 版本**参数作为 `3.0`
1. 在 "**文本**" 下，将 `string` 替换为以下 JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. 选择 "**执行**" 后，将在**响应正文**中输出生成的翻译。 应该会看到类似于下面的内容：
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. 启动 Visual Studio，并创建新的控制台应用程序。
1. 编辑 `*.csproj` 文件以添加 `<LangVersion>7.1</LangVersion>` 节点-此项指定C#为7.1。
1. 添加[Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 包11.0.2 版本。
1. 在 `Program.cs`中，将所有现有代码替换为以下代码：
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. 按 **F5** 运行程序。
1. 以下输出将打印到控制台。
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](translator-text-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文介绍了用于下载、安装和运行文本翻译容器的概念和工作流。 综上所述：

* 文本翻译为 Docker 提供多个 Linux 容器，封装各种语言对。
* 容器映像从 "容器预览" 注册表下载。
* 容器映像在 Docker 中运行。
* 您可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本翻译容器中的操作。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](translator-text-container-config.md)了解配置设置
* 请参阅[容器常见问题（FAQ）](../containers/container-faq.md) ，以解决与功能相关的问题。
