---
title: Azure IoT Edge 开发环境 | Microsoft Docs
description: 了解可帮助你创建 IoT Edge 模块的支持系统和第一方开发工具
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6fc2af0cbe770ee787da757966bbc1647717e5a
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302685"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>为 IoT Edge 准备开发和测试环境

Azure IoT Edge 将现有的业务逻辑移到 Edge 上运行的设备。 若要准备将应用程序和工作负载作为 [IoT Edge 模块](iot-edge-modules.md)运行，则需要将其生成为容器。 本文提供有关如何配置开发环境的指导，以便可以成功创建 IoT Edge 解决方案。 设置好开发环境后，即可了解如何[开发自己的 IoT Edge 模块](module-development.md)。

在任何 IoT Edge 解决方案中，至少需要考虑两台计算机。 一个是运行 IoT Edge 模块的 IoT Edge 设备本身。 另一个是用于生成、测试和部署模块的开发计算机。 本文重点介绍开发计算机。 出于测试目的，两台计算机可以是相同的。 你可以在开发计算机上运行 IoT Edge 并向其部署模块。

## <a name="operating-system"></a>操作系统

Azure IoT Edge 在一组特定的[支持操作系统](support.md)上运行。 对于 IoT Edge 的开发，可使用大多数可以运行容器引擎的操作系统。 容器引擎是开发计算机上的一项要求，用于将模块构建为容器并将其推送到容器注册表。 

若开发计算机无法运行 Azure IoT Edge，请继续阅读本文以了解有助于在本地进行测试和调试的[测试工具](#testing-tools)。 

开发计算机的操作系统不必与 IoT Edge 设备的操作系统相匹配。 但是，容器操作系统必须在开发计算机和 IoT Edge 设备之间保持一致。 例如，你可以在 Windows 计算机上开发模块并将其部署到 Linux 设备。 Windows 计算机需要运行 Linux 容器来为 Linux 设备构建模块。 

## <a name="container-engine"></a>容器引擎

IoT Edge 的核心概念是，通过将业务和云逻辑打包到容器中，可将其远程部署到设备。 若要构建容器，则需要在开发计算机上安装容器引擎。 

生产中 IoT Edge 设备唯一支持的容器引擎是 Moby。 但是，任何与开放容器计划（如 Docker）兼容的容器引擎均可构建 IoT Edge 模块映像。 

## <a name="development-tools"></a>开发工具

Visual Studio 和 Visual Studio Code 都具有附加扩展，可帮助开发 IoT Edge 解决方案。 这些扩展提供语言特定模板，以帮助创建和部署新的 IoT Edge 方案。 适用于 Visual Studio 和 Visual Studio Code 的 Azure IoT Edge 扩展可帮助编码、构建、部署和调试 IoT Edge 解决方案。 可创建包含多个模块的整个 IoT Edge 解决方案，并且扩展会在每次添加新模块时自动更新部署清单模板。 通过扩展，还可在 Visual Studio 或 Visual Studio Code 中管理 IoT 设备。 将模块部署到设备，监控状态，并在消息到达 IoT 中心时查看消息。 这两个扩展均使用 [IoT EdgeHub 开发工具](#iot-edgehub-dev-tool)来启用开发计算机上模块的本地运行和调试。 

若想要使用其他编辑器或从 CLI 进行开发，Azure IoT Edge 开发工具会提供命令，以便从命令行进行进行开发和测试。 可创建新的 IoT Edge 方案，构建模块映像，在模拟器中运行模块以及监控发送到 IoT 中心的消息。 

### <a name="visual-studio-code-extension"></a>Visual Studio Code 扩展

适用于 Visual Studio Code 的 Azure IoT Edge 扩展提供了基于编程语言（包括 C、C#、Java、Node.js 和 Python 以及 C# 中的 Azure Functions）构建的 IoT Edge 模块模板。 

有关详细信息和下载，请参阅 [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)（适用于 Visual Studio Code 的 Azure IoT Tools）。

除 IoT Edge 扩展外，你可能会发现安装其他扩展也有助于开发。 例如，可使用[适用于 Visual Studio Code 的 Docker 支持](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)来管理映像、容器和注册表。 此外，所有主要支持的语言都具有 Visual Studio Code 的扩展，可在开发模块时提供帮助。 

#### <a name="prerequisites"></a>必备组件

某些语言和服务的模块模板具有使用 Visual Studio Code 在开发计算机上构建项目文件夹时所必需的先决条件。

| 模块模板 | 先决条件 |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE 开发工具包 10](https://aka.ms/azure-jdks) <li> [设置 JAVA_HOME 环境变量](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js 模块生成器](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019年扩展

适用于 Visual Studio 的 Azure IoT Edge 工具提供 IoT Edge 模块模板基于C#和 c。 

有关详细信息和下载，请参阅[适用于 Visual Studio 2017 的 Azure IoT Edge 工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)或[适用于 Visual Studio 2019 的 Azure IoT Edge 工具](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)。

### <a name="iot-edge-dev-tool"></a>IoT Edge 开发工具

Azure IoT Edge 开发工具使用命令行功能简化了 IoT Edge 开发。 此工具提供用于开发、调试和测试模块的 CLI 命令。 无论是在计算机上手动安装依赖项还是使用 IoT Edge 开发容器，IoT Edge 开发工具都可以与开发系统配合使用。 

有关详细信息和入门，请参阅 [IoT Edge 开发工具 wiki](https://github.com/Azure/iotedgedev/wiki)。

## <a name="testing-tools"></a>测试工具

有几种测试工具可以帮助你更有效地模拟 IoT Edge 设备或调试模块。 下表显示了工具之间的高级别比较，各个部分更具体地描述了每个工具。 

生产部署仅支持 IoT Edge 运行时，但借助以下工具可模拟或轻松创建 IoT Edge 设备以进行开发和测试。 这些工具不是互斥的，可协同工作以获得完整的开发体验。 

| Tool | 也称为 | 支持的平台 | 最适用于 |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub 开发工具  | iotedgehubdev | Windows, Linux, MacOS | 模拟设备来调试模块。 |
| IoT Edge 开发容器 | microsoft/iotedgedev | Windows, Linux, MacOS | 开发而无需安装依赖项。 |
| 容器中的 IoT Edge 运行时 | iotedgec | Windows, Linux, MacOS, ARM | 在可能不支持运行时的设备上进行测试。 |
| IoT Edge 设备容器 | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | 使用多个 IoT Edge 设备大规模测试方案。 |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub 开发工具

Azure IoT EdgeHub 开发工具提供本地开发和调试体验。 该工具可在没有 IoT Edge 运行时的情况下帮助启动 IoT Edge 模块，以便可在本地创建、开发、测试、运行和调试 IoT Edge 模块和解决方案。 无需将映像推送到容器注册表并将其部署到设备进行测试。

IoT EdgeHub 开发工具旨在与 Visual Studio 和 Visual Studio Code 扩展以及 IoT Edge 开发工具协同工作。 该工具支持内部循环开发以及外部循环测试，还支持与 DevOps 工具集成。 

有关详细信息和安装，请参阅 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)。

### <a name="iot-edge-dev-container"></a>IoT Edge 开发容器

Azure IoT Edge 开发容器是一个 Docker 容器，具有 IoT Edge 开发所需的所有依赖项。 使用此容器可使用想要开发的任何语言（包括 C#、Python、Node.js 和 Java）轻松入门。 只需安装一个容器引擎，如 Docker 或 Moby，就可以将容器拉取到开发计算机上。 

有关详细信息，请参阅 [Azure IoT Edge 开发容器](https://hub.docker.com/r/microsoft/iotedgedev/)。

### <a name="iot-edge-runtime-in-a-container"></a>容器中的 IoT Edge 运行时

容器中的 IoT Edge 运行时提供了一个完整的运行时，该运行时将设备连接字符串作为环境变量。 通过此容器，可在可能不支持本机运行时的系统（如 MacOS）上测试 IoT Edge 模块和方案。 部署的任何模块都将在运行时容器之外启动。 如果希望运行时和任何已部署的模块存在于同一容器中，请考虑使用 IoT Edge 设备容器。

有关详细信息，请参阅[在容器中运行 Azure IoT Edge](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)。

### <a name="iot-edge-device-container"></a>IoT Edge 设备容器

IoT Edge 设备容器是一个完整的 IoT Edge 设备，可以在任何带有容器引擎的计算机上启动。 设备容器包括 IoT Edge 运行时和容器引擎本身。 容器的每个实例都是功能完备的自我预配 IoT Edge 设备。 只要存在到模块的网络路由，设备容器就支持模块的远程调试。 设备容器适合用于快速创建大量的 IoT Edge 设备来测试在缩放方案或 Azure 管道。 该设备容器还支持通过 helm 部署到 kubernetes。 

有关详细信息，请参阅 [Azure IoT Edge 设备容器](https://github.com/toolboc/azure-iot-edge-device-container)。

## <a name="devops-tools"></a>DevOps 工具

准备为大量生产方案开发大规模解决方案时，请利用现代 DevOps 原则，包括自动化、监控和简化的软件工程流程。 IoT Edge 具有支持 DevOps 工具的扩展，包括 Azure DevOps、Azure DevOps Projects 和 Jenkins。 如果要自定义现有管道或使用其他 DevOps 工具（如 CircleCI 或 TravisCI），则可以使用 IoT Edge 开发工具中包含的 CLI 功能执行此操作。

有关详细信息、指南和示例，请参阅以下页面：
* [向 Azure IoT Edge 进行持续集成和持续部署](how-to-ci-cd.md)
* [使用 Azure DevOps Projects 为 IoT Edge 创建 CI/CD 管道](how-to-devops-project.md)
* [Azure IoT Edge Jenkins 插件](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub 存储库](https://github.com/toolboc/IoTEdge-DevOps)
