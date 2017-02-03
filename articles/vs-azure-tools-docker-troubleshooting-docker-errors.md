---
title: "使用 Visual Studio 对 Windows 排查 Docker 客户端错误 | Microsoft Docs"
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
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Visual Studio Docker 开发故障排除

在使用 Visual Studio Tools for Docker 预览版时，可能会因预览特性而遇到一些问题。
以下是一些常见问题和解决方法。  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux 容器**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>调试 .NET Core Web 或控制台应用程序时，出现生成错误。  

这可能与未将项目所在的驱动器与 Docker For Windows 共享有关。  你可能会收到如下错误：

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
若要解决此问题，请右键单击 Docker for Windows 系统任务栏图标，然后选择“设置…”。  单击“共享驱动器”选项卡，并共享项目所在的驱动器号。

### <a name="windows-containers"></a>**Windows 容器**

以下内容特定于排查在 Windows 容器中调试 .NET Framework Web 和控制台应用程序时遇到的问题

### <a name="pre-requisites"></a>先决条件

1. 安装了 .NET Core 和 Docker（预览版）工作负荷的 Visual Studio 2017 RC（或更高版本）。
2. 安装了最新 Windows 更新修补程序的 Windows 10 周年更新。
3. Docker For Windows (Beta) - https://docs.docker.com/docker-for-windows/（版本 1.12.2-beta28 7813 或更高版本）。
4. 从 Docker For Windows 系统任务栏图标，选择“切换到 Windows 容器”。  重启计算机后，请确保此设置已保留。

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>单击“Docker: 调试项目”，生成错误“客户端版本 1.22 太旧。  支持的最低 API 版本为 1.24，请将客户端升级到更新版本”。

版本 1.13-RC2-beta31 (9123) 或更高版本的 Docker For Windows 需要使用版本 2.1 的 docker compose。   若要解决此问题，请更改项目中的 docker-compose*.yml 文件中版本“2”的所有匹配项。 Visual Studio 添加到项目的默认模板将在工具的未来版本中更新。 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>调试控制台应用程序时，控制台输出不会显示在 Visual Studio 的输出窗口中。

这是当前并非专为此方案设计的 Visual Studio 调试器 (msvsmon.exe) 存在的已知问题。  我们正在考虑在未来版本中对此提供支持。  若要在 Visual Studio 中查看控制台应用程序的输出，请使用“Docker: 启动项目”，它等效于“开始执行(不调试)”。

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>使用发布配置调试 Web 应用程序失败，显示“(403)禁止访问”错误。

若要解决此问题，请打开解决方案中的 web.release.config 文件，注释掉或删除以下行：

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>切换到 Windows 容器时，可能会看到一个错误对话框，指出“来自守护程序的错误响应: i/o 超时”。

可以在 https://github.com/docker/for-win/issues/178 中跟踪 Docker For Windows 的此问题。


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux 容器**

### <a name="unable-to-validate-volume-mapping"></a>无法验证卷映射
要与容器中的应用文件夹共享应用程序的源代码和二进制文件，必须进行卷映射。  具体的卷映射包含在 docker-compose.dev.debug.yml 和 docker-compose.dev.release.yml 文件中。 当文件在主机上发生更改时，容器将在相似的文件夹结构中反映这些更改。

若要启用卷映射，请从 Docker For Windows“小鲸鱼”任务栏图标打开“设置...”，然后选择“共享驱动器”选项卡。  确保托管项目的驱动器号以及 %USERPROFILE% 所在的驱动器号已共享，方法是选中它们并单击“应用”。

若要测试卷映射是否正常工作，在共享一个或多个驱动器后，在 Visual Studio 中执行“重新生成”并按 F5，或者从命令提示符尝试以下操作：

*在 Windows 命令提示符下*

> [!Note]
> 此示例假定 Users 文件夹位于“C”驱动器上并且已共享。
> 如果共享了一个不同的驱动器，请根据需要进行更新。

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*在 Linux 容器中*

```
/ # ls
```

你应看到 Users/Public 文件夹中的目录列表。
如果未显示任何文件，并且 /c/Users/Public 文件夹不为空，则卷映射未正确配置。

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

> [!Note]
> 使用 Linux VM 时，容器文件系统区分大小写。

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>生成："PrepareForBuild" 任务意外失败。

Microsoft.DotNet.Docker.CommandLine.ClientException：尝试连接时发生错误：

验证默认 Docker 主机是否正在运行。 打开命令提示符并执行以下命令：

```
docker info
```

如果这返回错误，则尝试启动 **Docker For Windows** 桌面应用。  如果桌面应用正在运行，则应该在任务栏中看到**魔比**图标。 右键单击该任务栏图标并打开“设置”。  单击“重置”选项卡，然后单击“重新启动 Docker...”。

##<a name="manually-upgrading-from-version-031-to-040"></a>手动从版本 0.31 升级到 0.40


1. 备份项目
1. 删除项目中的下列文件：

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. 关闭解决方案并从 .xproj 文件中删除下列行：

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. 重新打开解决方案
1. 从 Properties\launchSettings.json 文件中删除下列行：

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. 从 project.json 中的 publishOptions 部分删除与 Docker 相关的下列文件：

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. 卸载以前的版本并安装 Docker Tools 0.40，然后再次从你的 ASP.Net Core Web 或控制台应用程序的上下文菜单中选择“添加”->“Docker 支持”。 这会将新的必需 Docker 项目添加回你的项目。

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>尝试在容器中执行“添加”->“Docker 支持”时或者调试 (F5) ASP.NET Core 应用程序时出现一个错误对话框

在卸载和安装扩展后，有时候可能会看到 Visual Studio 的 MEF（托管扩展框架）缓存可能已损坏。 当发生此情况时，它可能会导致在添加 Docker 支持并且/或者尝试运行或调试 (F5) ASP.NET Core 应用程序时发生各种错误。 作为临时解决方法，执行以下步骤以删除并重新生成 MEF 缓存。

1. 关闭 Visual Studio 的所有实例
1. 打开 %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. 删除以下文件夹
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. 打开 Visual Studio
1. 再次尝试该方案



<!--HONumber=Dec16_HO2-->


