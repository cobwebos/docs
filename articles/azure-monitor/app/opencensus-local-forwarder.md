---
title: Azure 应用程序 Insights OpenCensus 本地转发器（预览）
description: 将 Python 和 Go 等语言的 OpenCensus 分布式跟踪与范围转发到 Azure Application Insights
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: bcf7ba495897eb1c9b40c78f00825e863390b5d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669958"
---
# <a name="local-forwarder-preview"></a>本地转发器（预览）

本地转发器是从各种框架中收集 Application Insights 或 [OpenCensus](https://opencensus.io/) 遥测数据并将其路由到 Application Insights 的代理。 它能够在 Windows 和 Linux 下运行。 也可以在 macOS 下运行，但目前并不正式支持这种运行方式。

## <a name="running-local-forwarder"></a>运行本地转发器

本地转发器是 [GitHub 上的一个开源项目](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)。 可通过多种方式在多个平台上运行本地转发器。

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows 服务

在 Windows 中运行本地转发器的最简单方法就是将其作为 Windows 服务安装。 服务版本随附了一个 Windows 服务可执行文件 (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*)，可轻松将其注册到操作系统中。

> [!NOTE]
> 本地转发器服务需要 .NET Framework 4.7 或更高版本。 如果未安装 .NET Framework 4.7，则该服务可完成安装，但不会启动。 若要获取最新版本的 .NET framework， **[请访问 .NET Framework 下载页](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** 。

1. 从 GitHub 上的[本地转发器发布页](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)下载 LF.WindowsServiceHost.zip 文件。

    ![本地转发器发布下载页的屏幕截图](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. 在这个易于演示的示例中，我们只需将该 .zip 文件解压缩到路径 `C:\LF-WindowsServiceHost`。

    若要注册该服务并将其配置为在系统引导时启动，请以管理员身份在命令行中运行以下命令：

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    应会收到以下响应：
    
    `[SC] CreateService SUCCESS`
    
    若要通过服务 GUI 检查新服务，请键入 ``services.msc``
        
     ![本地转发器服务的屏幕截图](./media/opencensus-local-forwarder/002-services.png)

3. **右键单击**新的本地转发器并选择“启动”。 现在，该服务将进入运行状态。

4. 默认情况下，创建的服务不提供任何恢复操作。 可以**单击右键**，并选择“属性” **“恢复”来配置发生服务故障时的自动响应方式。**  > 

    或者，如果你偏向于以编程方式设置发生故障时的自动恢复选项，可以使用：

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. 在 ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` 文件所在的同一位置（在本示例中为 ``C:\LF-WindowsServiceHost``），有一个名为 ``LocalForwarder.config`` 的文件。 这是一个基于 XML 的文件，可用于调整本地转发器的配置，并指定分布式跟踪数据要转发到的 Application Insights 资源的检测密钥。 

    通过编辑 ``LocalForwarder.config`` 文件添加检测密钥后，请务必重启“本地转发器服务”，使更改生效。
    
6. 若要确认所需的设置是否已准备就绪并且本地转发器正在按预期侦听跟踪数据，请检查 ``LocalForwarder.log`` 文件。 在该文件的底部，应会看到下图所示的结果：

    ![LocalForwarder.log 文件的屏幕截图](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>控制台应用程序

对于某些用例，以控制台应用程序的形式运行本地转发器可能更有利。 服务版本随附了控制台主机的以下可执行文件版本：
* 框架相关的 .NET Core 二进制文件 */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*。 运行此二进制文件需要安装 .NET Core 运行时；请参阅此下载[页](https://www.microsoft.com/net/download/dotnet-core/2.1)了解详细信息。
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* 适用于 x86 和 x64 平台的独立 .NET Core 二进制文件集。 不需要 .NET Core 运行时即可运行这些二进制文件。 */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*、 */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*。
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

与在 Windows 中一样，服务版本随附了控制台主机的以下可执行文件版本：
* 框架相关的 .NET Core 二进制文件 */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*。 运行此二进制文件需要安装 .NET Core 运行时；请参阅此下载[页](https://www.microsoft.com/net/download/dotnet-core/2.1)了解详细信息。

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* 适用于 linux-64 的独立 .NET Core 二进制文件集。 不需要 .NET Core 运行时即可运行此二进制文件。 */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*。

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

许多 Linux 用户希望将本地转发器作为守护程序运行。 Linux 系统随附了多种服务管理解决方案，例如 Upstart、sysv 或 systemd。 可以使用任何解决方案版本以最适合方案的方式运行本地转发器。

例如，让我们使用 systemd 创建一个守护程序服务。 我们将使用框架相关的版本，但使用独立的二进制文件也可以实现相同的效果。

* 创建以下名为 *localforwarder.service* 的服务文件，并将其置于 */lib/systemd/system* 中。
本示例假设用户名为 SAMPLE_USER，并且已将本地转发器框架相关的二进制文件从 *ConsoleHost/publish* 复制到 */home/SAMPLE_USER/LOCALFORWARDER_DIR*。

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* 运行以下命令，指示 systemd 在每次引导时都要启动本地转发器

```
systemctl enable localforwarder
```

* 运行以下命令，指示 systemd 立即启动本地转发器

```
systemctl start localforwarder
```

* 通过检查 /home/SAMPLE_USER/LOCALFORWARDER_DIR 目录中的 * *.log* 文件来监视服务。

### <a name="mac"></a>Mac
本地转发器可在 macOS 中工作，但目前不受正式支持。

### <a name="self-hosting"></a>自托管
本地转发器还作为 .NET Standard NuGet 包分发，可将它托管在你自己的 .NET 应用程序中。

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>配置本地转发器

* 运行本地转发器的某个自有主机（控制台主机或 Windows 服务主机）时，你会发现二进制文件旁边有一个 **LocalForwarder.config**。
* 自我托管本地转发器 NuGet 时，必须在代码中提供相同格式的配置（请参阅有关自我托管的部分）。 有关配置语法，请查看 GitHub 存储库中的 [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config)。 

> [!NOTE]
> 不同发行版的配置可能会有变化，请留意所用的版本。

## <a name="monitoring-local-forwarder"></a>监视本地转发器

跟踪将写出到文件系统中运行本地转发器的可执行文件的旁边（查看 * *.log* 文件）。 可在该可执行文件的旁边添加一个名为 *NLog.config* 的文件，以提供自己的配置来取代默认配置。 有关格式说明，请参阅[文档](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format)。

如果未提供配置文件（默认设置），则本地转发器将使用位于[此处](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)的默认配置。

## <a name="next-steps"></a>后续步骤

* [Open Census](https://opencensus.io/)
