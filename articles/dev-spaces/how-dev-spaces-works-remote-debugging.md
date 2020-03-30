---
title: 使用 Azure 开发人员空间远程调试代码的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 开发人员空间在 Azure 库伯内斯服务上进行远程调试的过程
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241395"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>使用 Azure 开发人员空间远程调试代码的工作原理

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序，并与您的团队协作处理 Azure Kubernetes 服务 （AKS） 群集。 项目在开发空间中运行后，Azure 开发人员空间提供了一种在 AKS 中附加和调试正在运行的应用程序的方法。

本文介绍了使用开发人员空间的远程调试的工作原理。

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET Core 和 Node.js 应用程序，您可以使用 Visual Studio 代码或可视化工作室调试直接在开发空间中运行的应用程序。 Visual Studio 代码和可视化工作室提供用于连接到开发空间、启动应用程序和附加调试器的工具。 运行`azds prep`后，您可以在可视化工作室代码或可视化工作室中打开项目。 视觉工作室代码或可视化工作室将生成自己的配置文件进行连接，这些文件与运行`azds prep`分开。 在 Visual Studio 代码或可视化工作室中，您可以设置断点并将应用程序启动到开发空间。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

当您使用 Visual Studio 代码或 Visual Studio 进行调试时，它们会以与运行`azds up`相同的方式处理启动和连接到开发空间。 此外，Visual Studio 代码和 Visual Studio 中的客户端工具都提供了一个附加参数，其中提供了用于调试的特定信息。 该参数包含调试器映像的名称、调试器在调试器映像中的位置以及应用程序容器中用于装载调试器文件夹的目标位置。

调试器映像由客户端工具自动确定。 它使用的方法类似于在运行`azds prep`时生成的 Dockerfile 和 Helm 图表期间使用的方法。 调试器安装在应用程序映像中后，使用`azds exec`运行调试器。

## <a name="next-steps"></a>后续步骤

要开始使用 Azure 开发人员空间进行远程调试项目，请参阅以下快速入门：

* [使用可视化工作室代码和 Java 快速迭代和调试][quickstart-java]
* [使用可视化工作室代码和 .NET 快速迭代和调试][quickstart-netcore]
* [使用可视化工作室代码和 Node.js 快速迭代和调试][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速迭代和调试][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
