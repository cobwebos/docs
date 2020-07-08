---
title: 用于语音容器的批处理工具包
titleSuffix: Azure Cognitive Services
description: 使用批处理工具包缩放语音容器请求。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4d0800ff8a35c5c91b067a85dfcc089f2e343d1f
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090888"
---
# <a name="batch-processing-kit-for-speech-containers"></a>用于语音容器的批处理工具包

使用批处理工具包来补充和扩展语音容器上的工作负荷。 此开源实用程序以容器的形式提供，可帮助跨任意数量的本地和基于云的语音容器终结点对大量音频文件进行批处理。 

:::image type="content" source="media/containers/general-diagram.png" alt-text="显示示例批处理套件容器工作流的关系图。":::

可在[GitHub](https://github.com/microsoft/batch-processing-kit)和[Docker 中心](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)免费使用 batch 工具包容器。 你只需要为你使用的语音容器[付费](speech-container-howto.md#billing)。

| 功能  | 说明  |
|---------|---------|
| 批处理音频文件分发     | 自动将大量文件分发到本地或基于云的语音容器终结点。 文件可以位于任何 POSIX 兼容卷上，包括网络文件系统。       |
| 语音 SDK 集成 | 将常见标志传递到语音 SDK，包括：假设、diarization、language、猥亵屏蔽。  |
|运行模式     | 在后台连续运行一次 batch 客户端，或创建音频文件的 HTTP 终结点。         |
| 容错 | 自动重试并继续运行，而不会丢失进度，并区分不同的错误，也不能重试。 |
| 终结点可用性检测 | 如果某个终结点变得不可用，则 batch 客户端将继续使用其他容器终结点转录。 再次变得可用之后，客户端将自动开始使用终结点。   |
| Endpoint 热交换 | 在运行时添加、删除或修改语音容器终结点，而不会中断批处理进度。 更新是即时的。 |
| 实时日志记录 | 实时记录尝试的请求、时间戳和失败原因，以及每个音频文件的语音 SDK 日志文件。 |

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令下载最新的 batch 工具包容器。

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>终结点配置

Batch 客户端使用 yaml 配置文件来指定本地容器终结点。 下面的示例可将以下示例写入到 `/mnt/my_nfs/config.yaml` 中。 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

此 yaml 示例在三台主机上指定三个语音容器。 第一个主机由 IPv4 地址指定，第二个主机在批处理客户端所在的同一 VM 上运行，第三个容器由另一个 VM 的 DNS 主机名指定。 `concurrency`该值指定可以在同一容器上运行的最大并发文件转录。 `rtf`（实时因子）值是可选的，可用于优化性能。
 
Batch 客户端可以动态检测终结点是否变为不可用（例如，由于容器重启或网络问题）以及何时变为可用。 脚本请求将不会发送到不可用的容器，客户端将继续使用其他可用的容器。 你可以随时添加、删除或编辑终结点，而不会中断批处理的进度。


## <a name="run-the-batch-processing-container"></a>运行批处理容器
  
> [!NOTE] 
> * 此示例对 `/my_nfs` 配置文件、输入、输出和日志目录使用相同的目录（）。 可以对这些文件夹使用托管或 NFS 装载的目录。
> * 使用运行客户端 `–h` 将列出可用的命令行参数及其默认值。 

使用 Docker `run` 命令启动容器。 这会在容器中启动一个交互式 shell。

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

运行 batch 客户端：  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

若要在单个命令中运行 batch 客户端和容器：

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

客户端将开始运行。 如果某个音频文件已在以前的运行中转录，则客户端将自动跳过该文件。 如果发生暂时性错误，则会随自动重试一起发送文件，并且可以区分想要客户端重试的错误。 出现脚本错误时，客户端将继续运行，并且可以重试而不会丢失进度。  

## <a name="run-modes"></a>运行模式 

批处理工具包提供三种模式，使用 `--run-mode` 参数。

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT`mode 转录将一批音频文件（从输入目录和可选文件列表）转换为输出文件夹。

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="以 oneshot 模式显示批处理套件容器处理文件的关系图。":::

1. 定义 batch 客户端将在文件中使用的语音容器终结点 `config.yaml` 。 
2. 将音频文件放入输入目录。  
3. 调用目录中的容器，该容器将开始处理这些文件。 如果已使用相同的输出目录（相同的文件名和校验和）在以前的运行中转录音频文件，则客户端将跳过该文件。 
4. 文件将被调度到步骤1中的容器终结点。
5. 日志和语音容器输出将返回到指定的输出目录。 

#### <a name="daemon"></a>[后台程序](#tab/daemon)

> [!TIP]
> 如果同时向输入目录添加了多个文件，则可以通过定期添加它们来提高性能。

`DAEMON`mode 转录指定文件夹中的现有文件，并在添加新的音频文件时连续转录这些文件。          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="显示批处理模式下的批处理套件容器处理文件的关系图。":::

1. 定义 batch 客户端将在文件中使用的语音容器终结点 `config.yaml` 。 
2. 调用输入目录中的容器。 Batch 客户端将开始监视传入文件的目录。 
3. 设置向输入目录传递连续的音频文件。 如果已使用相同的输出目录（相同的文件名和校验和）在以前的运行中转录音频文件，则客户端将跳过该文件。 
4. 检测到文件写入或 POSIX 信号后，将触发容器来做出响应。
5. 文件将被调度到步骤1中的容器终结点。
6. 日志和语音容器输出将返回到指定的输出目录。 

#### <a name="rest"></a>[REST](#tab/rest)

`REST`模式是一种 API 服务器模式，它提供了一组基本的 HTTP 终结点，用于批处理提交、状态检查和长时间轮询。 还使用 python 模块扩展启用编程，或以子模块的形式导入。

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="显示批处理模式下的批处理套件容器处理文件的关系图。":::

1. 定义 batch 客户端将在文件中使用的语音容器终结点 `config.yaml` 。 
2. 将 HTTP 请求请求发送到 API 服务器的一个终结点。 
        
    |终结点  |说明  |
    |---------|---------|
    |`/submit`     | 用于创建新批处理请求的终结点。        |
    |`/status`     | 用于检查批请求状态的终结点。 此连接将一直保持打开状态，直到批处理完成。       |
    |`/watch`     | 用于在批处理完成之前使用 HTTP 长轮询的终结点。        |

3. 音频文件将从输入目录上传。 如果已使用相同的输出目录（相同的文件名和校验和）在以前的运行中转录音频文件，则客户端将跳过该文件。 
4. 如果将请求发送到 `/submit` 终结点，则会将这些文件调度到步骤1中的容器终结点。
5. 日志和语音容器输出将返回到指定的输出目录。 

---

## <a name="logging"></a>Logging

> [!NOTE]
> 如果批处理客户端变得太大，则可能会定期覆盖*该文件。*

客户端在*run.log* `-log_folder` docker 命令中由自变量指定的目录中创建运行文件。 `run` 默认情况下，在调试级别捕获日志。 相同的日志会发送到 `stdout/stderr` ，并根据参数进行筛选 `-log_level` 。 此日志只是调试所必需的，或者，如果需要发送支持的跟踪，则为。 日志记录文件夹还包含每个音频文件的语音 SDK 日志。

指定的输出目录 `-output_folder` 将包含一个*run_summary.js*   文件，该文件将在每30秒或完成新转录时定期重写。 您可以使用此文件在批处理过程中检查进度。 它还将在批处理完成后包含每个文件的最终运行统计信息和最终状态。 当进程有干净退出时，批处理即已完成。 

## <a name="next-steps"></a>后续步骤

* [如何安装和运行容器](speech-container-howto.md)