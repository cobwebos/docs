---
title: "使用 Visual Studio 对 Windows 排查 Docker 客户端错误 | Microsoft 文档"
description: "在 Windows 上通过使用 Visual Studio 排查在使用 Visual Studio 创建 Web 应用并将其部署到 Docker 时遇到的问题。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Visual Studio Docker 开发故障排除

在使用 Visual Studio Tools for Docker 预览版时，可能会因预览特性而遇到一些问题。
以下是一些常见问题和解决方法。  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux 容器**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>调试 .NET Core Web 或控制台应用程序时，出现生成错误  

这可能与未将项目所在的驱动器与 Docker for Windows 共享有关。  可能会收到如下错误：

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
若要解决此问题，请执行以下操作：

1. 右键单击通知区域中的“Docker for Windows”，并选择“设置”。  
2. 选择“共享驱动器”，并共享项目所在的驱动器。

### <a name="windows-containers"></a>**Windows 容器**

以下是在 Windows 容器中调试 .NET Framework Web 和控制台应用程序时遇到的问题。

#### <a name="prerequisites"></a>先决条件

1. 必须安装具有 .NET Core 和 Docker 预览版工作负荷的 Visual Studio 2017 RC（或更高版本）。
2. 具有最新 Windows 更新修补程序的 Windows 10 周年更新。 具体而言，必须安装 [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016)。 
3. 必须安装 [Docker for Windows](https://docs.docker.com/docker-for-windows/)（内部版本 1.13.0 或更高版本）。
4. 必须选择“切换到 Windows 容器”。 在通知区域中，单击“Docker for Windows”，并选择“切换到 Windows 容器”。 重启计算机后，请确保此设置已保留。

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>调试控制台应用程序时，控制台输出未显示在 Visual Studio 的输出窗口中

这是当前并非专为此方案设计的 Visual Studio 调试器 (msvsmon.exe) 存在的已知问题。 将来的版本可能包含对此方案的支持。 若要在 Visual Studio 中查看控制台应用程序的输出，请使用“Docker: 启动项目”，它等效于“开始执行(不调试)”。

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>使用发布配置调试 Web 应用程序失败，显示“(403)禁止访问”错误

若要解决此问题，请打开解决方案中的 web.release.config，注释掉或删除以下行：

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux 容器**

#### <a name="unable-to-validate-volume-mapping"></a>无法验证卷映射
要与容器中的应用文件夹共享应用程序的源代码和二进制文件，必须进行卷映射。  具体的卷映射包含在 docker-compose.dev.debug.yml 和 docker-compose.dev.release.yml 中。 当文件在主机上发生更改时，容器会在相似的文件夹结构中反映这些更改。

若要启用卷映射，请执行以下操作：

1. 单击通知区域中的**小鲸鱼**，选择“设置”。
2. 选择“共享驱动器”。
3. 选择托管项目的驱动器和 %USERPROFILE% 所在的驱动器。
4. 单击“应用” 。

若要测试卷映射是否正常工作，请在共享一个或多个驱动器后，在 Visual Studio 中执行“重新生成”并选择 F5，或者从命令提示符运行以下代码。

> [!NOTE]
> 此示例假定 Users 文件夹位于驱动器 C 上并且已共享。
> 如果共享了一个不同的驱动器，请根据需要进行修改。

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

在 Linux 容器中运行以下代码。

```
/ # ls
```

应看到 Users/Public 文件夹中的目录列表。 如果未显示任何文件，并且 /c/Users/Public 文件夹不为空，则卷映射未正确配置。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

转到旋涡式星体目录，以查看 `/c/Users/Public` 目录的内容：

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> 使用 Linux VM 时，容器文件系统区分大小写。

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>生成：“PrepareForBuild”任务意外失败

Microsoft.DotNet.Docker.CommandLine.ClientException：尝试连接时发生错误。

验证默认 Docker 主机是否正在运行。 打开命令提示符并执行以下命令：

```
docker info
```

如果这返回错误，则尝试启动 **Docker for Windows** 桌面应用。 如果桌面应用正在运行，则应该在通知区域中看到**小鲸鱼**。 右键单击**小鲸鱼**并打开“设置”。 单击“重置”，并重新启动 Docker。

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>尝试在容器中添加 Docker 支持或调试 (F5) ASP.NET Core 应用程序时，显示错误对话框

卸载和安装扩展后，Visual Studio 的 Managed Extensibility Framework (MEF) 缓存可能已损坏。 当发生此情况时，它可能会导致在添加 Docker 支持和/或尝试运行或调试 (F5) ASP.NET Core 应用程序时显示各种错误消息。 暂时的解决方法是，执行以下步骤以删除和重新生成 MEF 缓存。

1. 关闭 Visual Studio 的所有实例。
1. 打开 %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\。
1. 删除以下文件夹：
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. 打开 Visual Studio。
1. 再次尝试该方案。
