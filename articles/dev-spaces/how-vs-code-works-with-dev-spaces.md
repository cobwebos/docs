---
title: 使用 Azure 开发人员空间的 Visual Studio Code 工作原理
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: 使用 Azure 开发人员空间的 Visual Studio Code 工作原理
keywords: Azure 开发人员空间，开发人员空格、 Docker、 Kubernetes、 Azure、 AKS，Azure Kubernetes 服务容器
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712145"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>使用 Azure 开发人员空间的 Visual Studio Code 工作原理

可以使用 Visual Studio Code 和[Azure 开发人员空间扩展][azds-extension]若要准备，运行和调试你的服务与 Azure 开发人员空格。 使用 Visual Studio Code 和 Azure 开发人员空间扩展，您可以：

* 用于运行和调试在 AKS 中的服务生成资产
* 适用于开发人员空间中运行 Java、 Node.js 和.NET Core 服务
* 直接调试您的开发空间中运行的 Java、 Node.js 和.NET Core 服务

## <a name="generate-assets"></a>生成资产

Visual Studio Code 和 Azure 开发人员空间扩展生成你的项目的以下资产：

* 使用 Maven 的 Java 应用程序、 Node.js 应用程序和.NET Core 应用程序的 Dockerfile
* Dockerfile 使用几乎任何语言的 helm 图表
* `azds.yaml`文件，即[Azure 开发人员空格配置文件][azds-yaml]为你的项目
* 一个`.vscode`文件夹与你的项目使用 Maven 的 Java 应用程序、 Node.js 应用程序和.NET Core 应用程序的 Visual Studio Code 启动配置

Dockerfile、 Helm 图表，并`azds.yaml`文件是运行时生成的相同资产`azds prep`。 这些文件还可在 Visual Studio code 外部运行您的项目中 AKS，例如运行`azds up`。 `.vscode`文件夹仅使用 Visual Studio code，若要从 Visual Studio Code 在 AKS 中运行你的项目。

## <a name="run-your-service-in-aks"></a>在 AKS 中运行你的服务

为你的项目生成资产后，可以从 Visual Studio Code 中的现有的开发空间运行 Java、 Node.js 和.NET Core 服务。 在中*调试*页上的 Visual Studio Code 中，可以调用中的启动配置`.vscode`目录运行你的项目。

必须创建 AKS 群集，并在 Visual Studio Code 外部群集中启用 Azure 开发人员空间。 例如，可以使用 Azure CLI 或 Azure 门户进行此设置。 可以重复使用现有 Dockerfile、 Helm 图表，并`azds.yaml`外部 Visual Studio Code 中，如通过运行生成的资产创建的文件`azds prep`。 如果不要重复使用 Visual Studio Code 外部生成的资产，则仍需要具有`.vscode`目录。 这`.vscode`目录可重新生成的 Visual Studio code 和 Azure 开发人员空间扩展并不会覆盖你的现有资产。

对于.NET Core 项目，你必须具有[C#扩展][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] ，以及已安装[Maven 安装并配置][maven]运行 Java从 Visual Studio Code 的服务。

## <a name="debug-your-service-in-aks"></a>调试你在 AKS 中的服务

启动你的项目后，您可以调试 Java、 Node.js 和.NET Core 服务也会直接从 Visual Studio Code 开发空间中运行。 中的启动配置`.vscode`目录提供了适用于开发人员空间中启用了调试运行服务的其他调试信息。 Visual Studio Code 还将附加到调试进程中正在运行的容器中开发的空格，从而可以设置断点、 检查变量，以及执行其他调试操作。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>使用 Azure 开发人员空间使用 Visual Studio 代码

您可以看到 Visual Studio Code 和 Azure 开发人员空间扩展在以下快速入门中使用 Azure 开发人员空格：

* [使用 Java 进行开发][quickstart-java]
* [使用.NET 进行开发][quickstart-netcore]
* [使用 Node.js 进行开发][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md