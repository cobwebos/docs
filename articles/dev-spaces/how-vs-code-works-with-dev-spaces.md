---
title: 可视化工作室代码如何与 Azure 开发空间配合使用
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: 了解可视化工作室代码和 Azure 开发人员空间如何帮助您调试和快速迭代 Kubernetes 应用程序
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240446"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>可视化工作室代码如何与 Azure 开发空间配合使用

可以使用可视化工作室代码和 Azure[开发空间扩展][azds-extension]使用 Azure 开发空间准备、运行和调试服务。 借助可视化工作室代码和 Azure 开发空间扩展，您可以：

* 生成用于在 AKS 中运行和调试服务的资产
* 在开发空间中运行 Java、Node.js 和 .NET 核心服务
* 直接调试在开发空间中运行的 Java、Node.js 和 .NET Core 服务

## <a name="generate-assets"></a>生成资产

可视化工作室代码和 Azure 开发空间扩展为项目生成以下资源：

* 使用 Maven、Node.js 应用程序和 .NET Core 应用程序的 Java 应用程序的 Dockerfile
* 几乎任何语言的头盔图表与 Dockerfile
* 文件`azds.yaml`，它是项目的[Azure 开发空间配置文件][azds-yaml]
* 具有`.vscode`使用 Maven、Node.js 应用程序和 .NET Core 应用程序的 Java 应用程序的项目的可视化工作室代码启动配置的文件夹

Dockerfile、Helm 图表和`azds.yaml`文件与运行时`azds prep`生成的资产相同。 这些文件还可用于 Visual Studio 代码之外，以在 AKS 中运行项目，例如`azds up`运行 。 该`.vscode`文件夹仅由 Visual Studio 代码使用，以从 Visual Studio 代码在 AKS 中运行项目。

## <a name="run-your-service-in-aks"></a>在 AKS 中运行服务

为项目生成资源后，可以从 Visual Studio Code 在现有开发空间中运行 Java、Node.js 和 .NET Core 服务。 在 Visual Studio 代码的*调试*页中，可以从`.vscode`目录中调用启动配置来运行项目。

您必须创建 AKS 群集，并在可视化工作室代码之外的群集中启用 Azure 开发空间。 例如，可以使用 Azure CLI 或 Azure 门户执行此设置。 您可以重用现有的 Dockerfile、Helm 图表和`azds.yaml`在 Visual Studio 代码之外创建的文件，例如运行`azds prep`生成的资产。 如果重复使用在 Visual Studio 代码之外生成的资产，则仍然需要有一个`.vscode`目录。 此`.vscode`目录可以通过 Visual Studio 代码和 Azure 开发人员空间扩展重新生成，并且不会覆盖现有资产。

对于 .NET Core 项目，必须安装[C# 扩展][csharp-extension]才能从 Visual Studio 代码运行 .NET 服务。 对于使用 Maven 的 Java 项目，必须安装[Azure 开发人员空间扩展的 Java 调试器][java-extension]，以及[安装 Maven 并将其配置为][maven]从 Visual Studio 代码运行 Java 服务。

## <a name="debug-your-service-in-aks"></a>在 AKS 中调试您的服务

启动项目后，可以直接从 Visual Studio Code 调试在开发空间中运行的 Java、Node.js 和 .NET Core 服务。 目录中的`.vscode`启动配置提供了用于在开发空间中启用调试的服务运行服务的其他调试信息。 Visual Studio Code 还附加到开发空间中正在运行的容器中的调试过程，允许您设置断点、检查变量和执行其他调试操作。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>将可视化工作室代码与 Azure 开发空间一起使用

您可以在以下快速入门中查看使用 Azure 开发空间的可视化工作室代码和 Azure 开发空间扩展：

* [使用可视化工作室代码和 Java 快速迭代和调试][quickstart-java]
* [使用可视化工作室代码和 .NET 快速迭代和调试][quickstart-netcore]
* [使用可视化工作室代码和 Node.js 快速迭代和调试][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
