---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 04/02/2019
ms.date: 04/23/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599436"
---
`Logging` 设置管理容器的 ASP.NET Core 日志记录支持。 可对容器使用用于 ASP.NET Core 应用程序的相同配置设置和值。 

容器支持以下日志记录提供程序：

|提供程序|目的|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。|
|[调试](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。|
|[磁盘](#disk-logging)|JSON 日志记录提供程序。 此日志记录提供程序将日志数据写入输出装入点。|

此容器命令以 JSON 格式输出装入到存储日志记录信息：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

此容器命令显示调试信息，前缀为`dbug`，而容器正在运行：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk 日志记录

`Disk` 日志记录提供程序支持以下配置设置：  

| 名称 | 数据类型 | 描述 |
|------|-----------|-------------|
| `Format` | String | 日志文件的输出格式。<br/> **注意：** 此值必须设置为 `json` 才能启用日志记录提供程序。 如果指定了此值，但未同时在实例化容器时指定输出装入点，则会发生错误。 |
| `MaxFileSize` | Integer | 日志文件的最大大小，以 MB 为单位。 如果当前日志文件的大小达到或超过此值，则日志记录提供程序会启动新的日志文件。 如果指定 -1，则日志文件的大小仅受输出装入点的最大文件大小（如果有）的限制。 默认值为 1。 |

有关配置 ASP.NET Core 日志记录支持的详细信息，请参阅[设置文件配置](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)。


