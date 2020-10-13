---
title: 使用 Azure Dev Spaces 远程调试代码的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍如何在 Azure Kubernetes Service 上进行远程调试的过程与 Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975036"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>使用 Azure Dev Spaces 远程调试代码的方式

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 在开发环境中运行项目后，Azure Dev Spaces 提供一种附加到 AKS 中正在运行的应用程序并对其进行调试的方式。

本文介绍如何使用 Dev Spaces 进行远程调试。

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET Core 和 Node.js 应用程序，可以使用 Visual Studio Code 或 Visual Studio 直接调试在开发环境中运行的应用程序。 Visual Studio Code 和 Visual Studio 提供了用于连接到开发人员空间、启动应用程序并附加调试器的工具。 运行后 `azds prep` ，可以在 Visual Studio Code 或 Visual Studio 中打开项目。 Visual Studio Code 或 Visual Studio 将生成自己的配置文件用于连接，而该文件是独立于运行的 `azds prep` 。 在 Visual Studio Code 或 Visual Studio 中，你可以设置断点并启动应用程序到你的开发环境。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

使用 Visual Studio Code 或 Visual Studio 启动应用程序进行调试时，它们将以与运行相同的方式处理启动和连接到开发环境 `azds up` 。 此外，Visual Studio Code 和 Visual Studio 中的客户端工具均提供了一个附加参数，其中包含用于调试的特定信息。 参数包含调试器映像的名称、中调试器的位置、调试器的映像中的位置，以及用于装载调试器文件夹的应用程序容器中的目标位置。

调试器映像由客户端工具自动确定。 它使用类似于在 Dockerfile 期间使用的方法，并在运行时生成 Helm 图表 `azds prep` 。 在应用程序的映像中装入调试器后，将使用运行该调试器 `azds exec` 。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](how-dev-spaces-works.md)
