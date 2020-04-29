---
title: 使用 Azure Dev Spaces 远程调试代码的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍如何在 Azure Kubernetes Service 上进行远程调试的过程与 Azure Dev Spaces
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241395"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>使用 Azure Dev Spaces 远程调试代码的方式

Azure Dev Spaces 提供多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务（AKS）群集上与团队进行协作。 在开发环境中运行项目后，Azure Dev Spaces 提供一种附加到 AKS 中正在运行的应用程序并对其进行调试的方式。

本文介绍如何使用 Dev Spaces 进行远程调试。

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET Core 和 node.js 应用程序，可以使用 Visual Studio Code 或 Visual Studio 调试直接在开发环境中运行的应用程序。 Visual Studio Code 和 Visual Studio 提供了用于连接到开发人员空间、启动应用程序并附加调试器的工具。 运行`azds prep`后，可以在 Visual Studio Code 或 Visual Studio 中打开项目。 Visual Studio Code 或 Visual Studio 将生成自己的配置文件用于连接，而该文件是`azds prep`独立于运行的。 在 Visual Studio Code 或 Visual Studio 中，你可以设置断点并启动应用程序到你的开发环境。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

使用 Visual Studio Code 或 Visual Studio 启动应用程序进行调试时，它们将以与运行`azds up`相同的方式处理启动和连接到开发环境。 此外，Visual Studio Code 和 Visual Studio 中的客户端工具均提供了一个附加参数，其中包含用于调试的特定信息。 参数包含调试器映像的名称、中调试器的位置、调试器的映像中的位置，以及用于装载调试器文件夹的应用程序容器中的目标位置。

调试器映像由客户端工具自动确定。 它使用类似于在 Dockerfile 期间使用的方法，并在运行`azds prep`时生成 Helm 图表。 在应用程序的映像中装入调试器后，将使用`azds exec`运行该调试器。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Dev Spaces 对项目进行远程调试，请参阅以下快速入门：

* [通过 Visual Studio Code 和 Java 快速循环访问和调试][quickstart-java]
* [通过 Visual Studio Code 和 .NET 快速循环访问和调试][quickstart-netcore]
* [通过 Visual Studio Code 和 node.js 快速循环访问和调试][quickstart-node]
* [通过 Visual Studio 和 .NET Core 快速循环访问和调试][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
