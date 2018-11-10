---
title: 使用 Visual Studio 对 Windows 排查 Docker 客户端错误 | Microsoft 文档
description: 在 Windows 上通过使用 Visual Studio 2017 排查在使用 Visual Studio 创建 Web 应用并将其部署到 Docker 时遇到的问题。
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968625"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>排查使用 Docker 进行的 Visual Studio 2017 开发

使用 Visual Studio Tools for Docker 时，可能会在生成或调试应用程序过程中遇到问题。 以下是一些常见的故障排除步骤。

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>未启用卷共享。 启用“Docker CE for Windows”设置中的卷共享（仅 Linux 容器）

若要解决此问题，请执行以下操作：

1. 右键单击通知区域中的“Docker for Windows”，并选择“设置”。
1. 选择“共享驱动器”，并共享系统驱动器和项目所在的驱动器。

> [!NOTE]
> 如果文件显示“已共享”，可能仍需要单击对话框底部的“重置凭据...”链接，以便重新启用卷共享。 若要在重置凭据后继续，可能必须重启 Visual Studio。

![共享驱动器](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>装载被拒绝

使用 Docker for macOS 时，可能会遇到引用文件夹 /usr/local/share/dotnet/sdk/NuGetFallbackFolder 错误。 将文件夹添加到 Docker 中的“文件共享”选项卡。

## <a name="unable-to-start-debugging"></a>无法开始调试

其中一个原因可能与在用户配置文件的文件夹中有过时调试组件有关。 请执行以下命令来删除这些文件夹，以便在下次调试会话上下载最新调试组件。

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>调试应用程序时特定于网络的错误

尝试执行可从[清理容器主机网络](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking)下载的脚本，此操作会刷新主机上的网络相关组件。


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub 存储库

有关可能会遇到的其他任何问题，请参阅 [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) 问题。