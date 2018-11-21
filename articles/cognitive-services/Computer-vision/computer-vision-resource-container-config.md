---
title: 配置容器
titlesuffix: Computer Vision - Cognitive Services - Azure
description: 计算机视觉中的容器配置设置。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 8df293878486a9cd4ded6e899871c30498c4b68f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634893"
---
# <a name="configure-containers"></a>配置容器

计算机视觉为“识别文本”容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。

## <a name="configuration-settings"></a>配置设置

计算机视觉容器中的配置设置是分层的，所有容器基于以下顶级结构使用共享的层次结构：

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [身份验证](#authentication-configuration-settings)
* [计费](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [日志记录](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

可以使用[环境变量](#configuration-settings-as-environment-variables)或[命令行参数](#configuration-settings-as-command-line-arguments)在实例化计算机视觉容器中的容器时指定配置设置。

环境变量值替代命令行参数值，而这些参数值又替代容器映像的默认值。 换言之，如果在环境变量和命令行参数中为同一配置设置（例如 `Logging:Disk:LogLevel`）指定不同的值，然后实例化容器，则实例化的容器将使用环境变量中的值。

### <a name="configuration-settings-as-environment-variables"></a>环境变量形式的配置设置

可以使用 [ASP.NET Core 环境变量语法](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment)来指定配置设置。

容器在实例化时读取用户环境变量。 如果存在环境变量，则环境变量的值将替代指定的配置设置的默认值。 使用环境变量的好处是可以在实例化容器之前设置多个配置设置，并且多个容器可以自动使用同一组配置设置。

例如，以下命令使用环境变量将控制台日志记录级别配置为 [LogLevel.Information](https://msdn.microsoft.com)，然后从“识别文本”容器映像实例化容器。 环境变量的值将替代默认配置设置。

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>命令行参数形式的配置设置

可以使用 [ASP.NET Core 命令行参数语法](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments)来指定配置设置。

可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令的可选 `ARGS` 参数（用于从下载的容器映像实例化容器）中指定配置设置。 使用命令行参数的好处是每个容器都可以使用不同的自定义配置设置集。

例如，以下命令从“识别文本”容器映像实例化容器，并将控制台日志记录级别配置为 LogLevel.Information，从而替代默认配置设置。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 配置设置指定 Azure 上用于跟踪容器的计费信息的计算机视觉资源的配置密钥。 必须为此配置设置指定值，并且该值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的计算机视觉资源的有效配置密钥。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-configuration-setting) 配置设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](computer-vision-how-to-install-containers.md#billing)。

## <a name="applicationinsights-configuration-settings"></a>ApplicationInsights 配置设置

使用 `ApplicationInsights` 节中的配置设置可以向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。 Application Insights 提供对容器的深入监视，深度可达代码级别。 可以轻松监视容器的可用性、性能和使用情况。 还可以快速识别和诊断容器中的错误，而无需等待用户报告这些错误。

下表描述了 `ApplicationInsights` 节支持的配置设置。

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `InstrumentationKey` | String | 容器遥测数据要发送到的 Application Insights 实例的检测密钥。 有关详细信息，请参阅 [Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 |

## <a name="authentication-configuration-settings"></a>Authentication 配置设置

`Authentication` 配置设置为容器提供 Azure 安全选项。 尽管此节中的配置设置可用，但“识别文本”容器并不使用此节。

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 配置设置指定 Azure 上用于跟踪容器账单信息的计算机视觉资源的终结点 URI。 必须为此配置设置指定值，并且该值必须是 Azure 上计算机视觉资源的有效终结点 URI。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-configuration-setting) 配置设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](computer-vision-how-to-install-containers.md#billing)。

## <a name="eula-configuration-setting"></a>Eula 配置设置

`Eula` 配置设置表示已接受容器的许可条款。 必须为此配置设置指定值，并且该值必须设置为 `accept`。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-configuration-setting) 配置设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](computer-vision-how-to-install-containers.md#billing)。

## <a name="fluentd-configuration-settings"></a>Fluentd 配置设置

`Fluentd` 节管理 [Fluentd](https://www.fluentd.org)（用于统一日志记录的开源数据收集器）的配置设置。 计算机视觉容器包含一个 Fluentd 日志记录提供程序，让容器向 Fluentd 服务器写入日志和（可选）指标数据。

下表描述了 `Fluentd` 节支持的配置设置。

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `Host` | String | Fluentd 服务器的 IP 地址或 DNS 主机名。 |
| `Port` | Integer | Fluentd 服务器的端口。<br/> 默认值为 24224。 |
| `HeartbeatMs` | Integer | 检测信号间隔，以毫秒为单位。 如果在此间隔过期之前未发送事件流量，则将检测信号发送到 Fluentd 服务器。 默认值为 60000 毫秒（1 分钟）。 |
| `SendBufferSize` | Integer | 为发送操作分配的网络缓冲空间，以字节为单位。 默认值为 32768 字节（32 KB）。 |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | 与 Fluentd 服务器建立 SSL/TLS 连接的超时值，以毫秒为单位。 默认值为 10000 毫秒（10 秒）。<br/> 如果 `UseTLS` 设置为 false，则会忽略此值。 |
| `UseTLS` | Boolean | 指示容器是否应使用 SSL/TLS 来与 Fluentd 服务器通信。 默认值为 false。 |

## <a name="logging-configuration-settings"></a>Logging 配置设置

`Logging` 配置设置管理容器的 ASP.NET Core 日志记录支持。 可对容器使用用于 ASP.NET Core 应用程序的相同配置设置和值。 计算机视觉容器支持以下日志记录提供程序：

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。
* [调试](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。
* 磁盘  
  JSON 日志记录提供程序。 此日志记录提供程序将日志数据写入输出装入点。  
  `Disk` 日志记录提供程序支持以下配置设置：  

  | 名称 | 数据类型 | Description |
  |------|-----------|-------------|
  | `Format` | String | 日志文件的输出格式。<br/> **注意：** 此值必须设置为 `json` 才能启用日志记录提供程序。 如果指定了此值，但未同时在实例化容器时指定输出装入点，则会发生错误。 |
  | `MaxFileSize` | Integer | 日志文件的最大大小，以 MB 为单位。 如果当前日志文件的大小达到或超过此值，则日志记录提供程序会启动新的日志文件。 如果指定 -1，则日志文件的大小仅受输出装入点的最大文件大小（如果有）的限制。 默认值为 1。 |

有关配置 ASP.NET Core 日志记录支持的详细信息，请参阅[设置文件配置](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)。

## <a name="mounts-configuration-settings"></a>Mounts 配置设置

计算机视觉提供的 Docker 容器在设计上是无状态且不可变的。 换言之，在容器中创建的文件将存储在可写的容器层中，该层只在运行容器时才会保存，并且不能轻松对其进行访问。 如果停止或删除该容器，将会销毁该容器以及其中创建的文件。

但是，由于它们是 Docker 容器，因此你可以使用 Docker 存储选项（例如卷和绑定装入点）来读取和写入容器外部保存的数据（如果该容器支持这些操作）。 有关如何指定和管理 Docker 存储选项的详细信息，请参阅[管理 Docker 中的数据](https://docs.docker.com/storage/)。

> [!NOTE]
> 通常不需要更改这些配置设置的值。 指定容器的输入和输出装入点时，可以使用这些配置设置中指定的值作为目标。 有关指定输入和输出装入点的详细信息，请参阅[输入和输出装入点](#input-and-output-mounts)。

下表描述了 `Mounts` 节支持的配置设置。

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `Input` | String | 输入装入点的目标。 默认值为 `/input`。 |
| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 |

### <a name="input-and-output-mounts"></a>输入和输出装入点

默认情况下，每个容器可以支持一个输入装入点（容器可从中读取数据）和一个输出装入点（容器可将数据写入到其中）。 但是，容器并非一定要支持输入或输出装入点。每个容器可以同时使用输入和输出装入点来实现特定于容器的目的，此外，可以使用计算机视觉容器支持的日志记录选项。

“识别文本”容器不支持输入装入点，但可以选择性地支持输出装入点。

可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令（用于从下载的容器映像实例化容器）中指定 `--mount` 选项，来指定输入装入点或输出装入点。 默认情况下，输入装入点使用 `/input` 作为目标，输出装入点使用 `/output` 作为目标。 可在 `--mount` 选项中指定 Docker 容器主机可用的任何 Docker 存储选项。

例如，以下命令在主机上的 `D:\Output` 文件夹中定义一个 Docker 绑定装入点作为输出装入点，然后从“识别文本”容器映像实例化容器，并将 JSON 格式的日志文件保存到输出装入点。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
