---
title: Visual Studio Code 如何处理 Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code 如何处理 Azure Dev Spaces
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: 50ce5e90e091ea72761cd7513d2508c657981e60
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279885"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code 如何处理 Azure Dev Spaces

您可以使用 Visual Studio Code 和[Azure Dev Spaces 扩展][azds-extension]通过 Azure Dev Spaces 准备、运行和调试您的服务。 通过 Visual Studio Code 和 Azure Dev Spaces 扩展，可以：

* 为在 AKS 中运行和调试服务生成资产
* 在开发环境中运行 Java、node.js 和 .NET Core 服务
* 直接调试在开发人员空间中运行的 Java、node.js 和 .NET Core 服务

## <a name="generate-assets"></a>生成资产

Visual Studio Code 和 Azure Dev Spaces 扩展为你的项目生成以下资产：

* 使用 Maven、node.js 应用程序和 .NET Core 应用程序的 Java 应用程序的 dockerfile
* 使用 Dockerfile 的几乎任何语言的 Helm 图
* 一个 `azds.yaml` 文件，该文件是项目的[Azure Dev Spaces 配置文件][azds-yaml]
* 一个 `.vscode` 文件夹，其中包含使用 Maven、node.js 应用程序和 .NET Core 应用程序的 Java 应用程序的项目 Visual Studio Code 启动配置

Dockerfile、Helm 图表和 `azds.yaml` 文件是运行 `azds prep`时生成的相同资产。 也可以在 Visual Studio code 之外使用这些文件在 AKS 中运行项目，例如运行 `azds up`。 `.vscode` 文件夹仅由 Visual Studio code 用来从 Visual Studio Code 在 AKS 中运行项目。

## <a name="run-your-service-in-aks"></a>在 AKS 中运行你的服务

为项目生成资产后，可以在 Visual Studio Code 的现有开发人员空间中运行 Java、node.js 和 .NET Core 服务。 在 Visual Studio Code 的 "*调试*" 页中，可以从 `.vscode` 目录调用启动配置来运行项目。

您必须创建 AKS 群集，并在 Visual Studio Code 外启用群集中的 Azure Dev Spaces。 例如，可以使用 Azure CLI 或 Azure 门户来执行此设置。 您可以重复使用 Visual Studio Code 之外创建的现有 Dockerfile、Helm 图表和 `azds.yaml` 文件，例如通过运行 `azds prep`生成的资产。 如果确实要重复使用在 Visual Studio Code 之外生成的资产，仍需要具有 `.vscode` 目录。 此 `.vscode` 目录可通过 Visual Studio code 和 Azure Dev Spaces 扩展重新生成，不会覆盖现有资产。

对于 .net Core 项目，必须安装[ C#扩展][csharp-extension]，才能从 Visual Studio Code 运行 .net 服务。 此外，对于使用 Maven 的 Java 项目，必须安装[java Azure Dev Spaces 调试器][java-extension]，[并安装 Maven 并][maven]将其配置为从 Visual Studio Code 运行 java 服务。

## <a name="debug-your-service-in-aks"></a>在 AKS 中调试你的服务

启动项目后，可以直接从 Visual Studio Code 调试开发环境中运行的 Java、node.js 和 .NET Core 服务。 "`.vscode`" 目录中的 "启动配置" 提供了额外的调试信息，用于在开发人员空间中运行启用了调试的服务。 Visual Studio Code 还会附加到开发空间中正在运行的容器中的调试进程，使你可以设置断点、检查变量和执行其他调试操作。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>将 Visual Studio Code 与 Azure Dev Spaces 配合使用

在以下快速入门中，可以看到 Visual Studio Code 和 Azure Dev Spaces 扩展使用 Azure Dev Spaces：

* [通过 Visual Studio Code 和 Java 快速循环访问和调试][quickstart-java]
* [通过 Visual Studio Code 和 .NET 快速循环访问和调试][quickstart-netcore]
* [通过 Visual Studio Code 和 node.js 快速循环访问和调试][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
