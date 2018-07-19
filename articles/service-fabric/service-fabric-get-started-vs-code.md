---
title: Azure Service Fabric 和 VS Code 入门 | Microsoft Docs
description: 本文概述如何使用 Visual Studio Code 创建 Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 367829c269bd1d96e6aa5fab1be008483a4ab5ab
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115284"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric for Visual Studio Code

[适用于 VS Code 的 Service Fabric Reliable Services 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)提供所需的工具用于在 Windows、Linux 和 macOS 操作系统上创建、生成和调试 Service Fabric 应用程序。

本文概述该扩展的要求和设置，以及该扩展提供的各个命令的用法。 

> [!IMPORTANT]
> 可在 Windows 计算机上开发 Service Fabric Java 应用程序，但只能将其部署到 Azure Linux 群集。 Windows 不支持调试 Java 应用程序。

## <a name="prerequisites"></a>先决条件

必须在所有环境中安装以下必备组件。

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman 生成器 -- 为应用程序安装相应的生成器

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

必须为 Java 开发安装以下必备组件：

* [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)（版本 1.8）
* [Gradle](https://gradle.org/install/)
* 调试 Java 服务时所需的[适用于 Java VS Code 的调试程序扩展](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)。 仅支持在 Linux 上调试 Java 服务。 可通过以下方法之一安装该扩展：在 VS Code 中的**活动栏**上单击“扩展”图标并搜索该扩展，或者从 VS Code 市场安装。

必须为 .NET Core/C# 开发安装以下必备组件：

* [.NET Core](https://www.microsoft.com/net/learn/get-started)（2.0.0 或更高版本）
* 调试 C# 服务时所需的 [C# for Visual Studio Code (Powered by OmniSharp) VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 可通过以下方法之一安装该扩展：在 VS Code 中的**活动栏**上单击“扩展”图标并搜索该扩展，或者从 VS Code 市场安装。

## <a name="setup"></a>设置

1. 打开 VS Code。
2. 在 VS Code 左侧的**活动栏**中单击“扩展”图标。 搜索“Service Fabric”。 单击 Service Fabric Reliable Services 扩展对应的“安装”。

## <a name="commands"></a>命令
适用于 VS Code 的 Service Fabric Reliable Services 扩展提供许多命令用于帮助开发人员创建和部署 Service Fabric 项目。 可以从**命令面板**调用命令：按 `(Ctrl + Shift + p)`，在输入栏中键入命令名称，然后从提示列表中选择所需的命令即可。 

* Service Fabric: 创建应用程序 
* Service Fabric: 发布应用程序 
* Service Fabric: 部署应用程序 
* Service Fabric: 删除应用程序  
* Service Fabric: 生成应用程序 
* Service Fabric: 清理应用程序 

### <a name="service-fabric-create-application"></a>Service Fabric: 创建应用程序

“Service Fabric: 创建应用程序”命令在当前工作区中创建新的 Service Fabric 应用程序。 根据开发计算机上安装的 yeoman 生成器，可以创建多种类型的 Service Fabric 应用程序，包括 Java、C#、容器和来宾项目。 

1.  选择“Service Fabric: 添加服务”命令
2.  选择新 Service Fabric 应用程序的类型。 
3.  输入要创建的应用程序的名称
3.  选择要添加到 Service Fabric 应用程序的服务的类型。 
4.  遵照提示将服务命名。 
5.  新 Service Fabric 应用程序将显示在工作区中。
6.  打开新应用程序所在的文件夹，使其成为工作区中的根文件夹。 可以继续在此处执行命令。

### <a name="service-fabric-add-service"></a>Service Fabric: 添加服务
“Service Fabric: 添加服务”命令将新服务添加到现有的 Service Fabric 应用程序。 要将服务添加到的应用程序必须是工作区的根目录。 

1.  选择“Service Fabric: 添加服务”命令。
2.  选择当前 Service Fabric 应用程序的类型。 
3.  选择要添加到 Service Fabric 应用程序的服务的类型。 
4.  遵照提示将服务命名。 
5.  新服务将显示在项目目录中。 

### <a name="service-fabric-publish-application"></a>Service Fabric: 发布应用程序
“Service Fabric: 发布应用程序”命令在远程群集上部署 Service Fabric 应用程序。 目标群集可以是安全的群集，也可以是不安全的群集。 如果未在 Cloud.json 中设置参数，应用程序将部署到本地群集。

1.  首次生成应用程序时，将在项目目录中生成 Cloud.json 文件。
2.  在 Cloud.json 文件中，输入要连接到的群集的值。
3.  选择“Service Fabric: 发布应用程序”命令。
4.  使用 Service Fabric Explorer 查看目标群集，确认已安装该应用程序。 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: 部署应用程序(Localhost)
“Service Fabric: 部署应用程序”命令将 Service Fabric 应用程序部署到本地群集。 使用该命令之前，请确保本地群集正在运行。 

1.  选择“Service Fabric: 部署应用程序”命令
2.  使用 Service Fabric Explorer (http://localhost:19080/Explorer) 查看本地群集，确认已安装该应用程序。 此过程可能需要一段时间，请耐心等待。
3.  也可以在未在 Cloud.json 文件中设置任何参数的情况下，使用“Service Fabric: 发布应用程序”命令部署到本地群集。

> [!NOTE]
> Windows 计算机上不支持将 Java 应用程序部署到本地群集。

### <a name="service-fabric-remove-application"></a>Service Fabric: 删除应用程序
“Service Fabric: 删除应用程序”命令将 Service Fabric 应用程序从先前使用 VS Code 扩展将它部署到的群集中删除它。 

1.  选择“Service Fabric: 删除应用程序”命令。
2.  使用 Service Fabric Explorer 查看该群集，确认已删除该应用程序。 此过程可能需要一段时间，请耐心等待。

### <a name="service-fabric-build-application"></a>Service Fabric: 生成应用程序
“Service Fabric: 生成应用程序”命令可以生成 Java 或 C# Service Fabric 应用程序。 

1.  执行此命令之前，请确保在应用程序根文件夹中操作。 该命令会识别应用程序的类型（C# 或 Java），并相应地生成应用程序。
2.  选择“Service Fabric: 生成应用程序”命令。
3.  生成过程的输出将写入集成式终端。

### <a name="service-fabric-clean-application"></a>Service Fabric: 清理应用程序
“Service Fabric: 清理应用程序”命令会删除生成过程生成的所有 jar 文件和本机库。 此命令仅对 Java 应用程序有效。 

1.  执行此命令之前，请确保在应用程序根文件夹中操作。 
2.  选择“Service Fabric: 清理应用程序”命令。
3.  清理过程的输出将写入集成式终端。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 VS Code 开发和调试 C# Service Fabric 应用程序](./service-fabric-develop-csharp-applications-with-vs-code.md)。
* 了解如何[使用 VS Code 开发和调试 Java Service Fabric 应用程序](./service-fabric-develop-java-applications-with-vs-code.md)。
