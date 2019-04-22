---
title: 如何将 OPC 保管库模块从零开始的 Azure 部署 |Microsoft Docs
description: 如何部署从零开始的 OPC 保管库。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496890"
---
# <a name="deploy-opc-vault-from-scratch"></a>从头开始部署 OPC 保管库

OPC 保管库是可以配置、注册和管理云中 OPC UA 服务器与客户端应用程序的证书生命周期的微服务。 本文介绍如何部署从零开始的 OPC 保管库。

## <a name="configuration-and-environment-variables"></a>配置和环境变量

服务配置存储在 appsettings.ini 中使用 ASP.NET Core 配置适配器。 INI 格式允许存储中以可读的格式，带批注的值。
应用程序还支持插入的环境变量，例如凭据和网络的详细信息。 （本部分中最初的标题为 TODO 配置和环境变量）。

存储库中的配置文件引用，需要至少一次创建一些环境变量。 根据您的操作系统和 IDE，有几种方法来管理环境变量：

- Windows 用户 env var setup.cmd 脚本需要准备好并执行只需一次。 执行时，设置将保留在终端会话和重新启动。

- 对于 Linux 和 OSX 的环境，env var 安装脚本需要每次打开新控制台执行。 具体取决于操作系统和终端中，有方法来持久值全局，保存有关这些页面可帮助的详细信息：

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio：环境变量可以从 Visual Studio 中，在项目属性下，还设置，在左窗格中选择要转到可以在其中添加多个变量部分的"配置属性"和"环境"。

- IntelliJ Rider:可以在每个运行配置，类似于 IntelliJ IDEA 中设置环境变量 https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>运行和调试使用 Visual Studio

Visual Studio，您可以快速而无需使用命令提示符处，而无需配置任何内容在 IDE 外打开应用程序。

使用 Visual Studio 2017 的步骤：

1. 打开的解决方案使用`iot-opc-gds-service.sln`文件。

1. 加载解决方案时，右键单击`WebService`项目中，选择，并转到`Debug`部分。

1. 在同一部分中，定义所需的环境变量。

1. 按**F5**，或**运行**图标。 Visual Studio 应打开浏览器中以 JSON 格式显示服务状态。

## <a name="run-and-debug-with-intellij-rider"></a>运行和调试使用 IntelliJ Rider

1. 打开的解决方案使用`iot-opc-gds-service.sln`文件。

1. 加载解决方案时，请转到`Run > Edit Configurations`并创建新的`.NET Project`配置。

1. 在配置中，选择 web 服务项目。

1. 保存设置并运行从 IDE 工具栏中创建的配置。

1. 应看到服务启动 IntelliJ 运行窗口的详细信息，例如 URL 加上该服务中运行 web 服务中的消息日志。

## <a name="build-and-run-from-the-command-line"></a>从命令行生成并运行

脚本文件夹包含一些常见任务的脚本：

- `build`：编译的所有项目并运行测试。

- `compile`：编译的所有项目。

- `run`：编译项目并运行在 Windows 中的提升权限提示以运行 web 服务的服务。

脚本检查环境变量设置。 可以在你的 OS 中的全局设置环境变量，也可以使用脚本文件夹中的"env var 设置"脚本。

### <a name="sandbox"></a>沙盒

这些脚本假定你使用.NET Core 和 Docker 配置开发环境。 您可以避免安装.NET Core 和安装仅 Docker 并使用命令行参数`--in-sandbox`(或缩写形式`-s`)，例如：

- `build --in-sandbox`：执行 Docker 容器内部的生成任务 (短格式`build -s`)。

- `compile --in-sandbox`：执行 Docker 容器内部的编译任务 (短格式`compile -s`)。

- `run --in-sandbox`：启动 Docker 容器内部的服务 (短格式`run -s`)。

用于在沙箱的 Docker 映像托管在 Docker Hub[此处](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet)。

## <a name="package-the-application-to-a-docker-image"></a>打包到 Docker 映像应用程序

`scripts`文件夹包括 docker 子文件夹具有的服务打包到 Docker 映像所需的文件：

- `Dockerfile`：Docker 映像的规范。
- `build`：生成 Docker 容器并将图像存储在本地注册表中。
- `run`：从本地注册表中存储的映像运行 Docker 容器。
- `content`：包含文件复制到映像，包括入口点脚本的文件夹。

## <a name="azure-iot-hub-setup"></a>Azure IoT 中心设置

若要使用微服务，设置 Azure IoT 中心的开发和集成测试。

该项目包括一些 Bash 脚本来帮助你进行此安装程序：

- 创建新的 IoT 中心： `./scripts/iothub/create-hub.sh`

- 列出现有中心： `./scripts/iothub/list-hubs.sh`

- 显示 IoT 中心的详细信息 （例如，键中）： `./scripts/iothub/show-hub.sh`

和在用例中，有多个 Azure 订阅：

- 显示订阅列表： `./scripts/iothub/list-subscriptions.sh`

- 更改当前订阅： `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>开发设置

### <a name="net-setup"></a>.NET 安装程序

通过管理项目工作流[.NET Core](https://dotnet.github.io) 1.x 中，您需要将安装在你的环境，以便可以运行所有脚本，并确保你的 IDE 按预期方式工作。

我们还提供了[Java 版本](https://github.com/Azure/iot-opc-gds-service-dotnet)此项目和其他 Azure IoT PC 组件。

### <a name="ide"></a>IDE

下面是 ide 的一些可用于处理 Azure IoT 电脑上:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git 安装

该项目包括 Git 挂钩，若要自动执行一些检查之前接受代码更改。 可以手动运行测试，或让 CI 平台以运行测试。 我们使用以下 Git 挂钩来自动运行所有测试，再将代码更改发送到 GitHub 并加快开发工作流。

如果任何时候你想要删除此挂钩，只需删除安装下的文件`.git/hooks`。 您还可以不提交前挂钩使用`--no-verify`选项。

#### <a name="pre-commit-hook-with-sandbox"></a>与沙盒配合使用的提交前挂钩

若要设置所包含的挂钩，打开 Windows/Linux/MacOS 控制台并执行：

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

使用此配置，签入文件时，Git 会验证应用程序通过所有测试，运行生成和 Docker 容器内部的所有开发要求都配置的测试。

#### <a name="pre-commit-hook-without-sandbox"></a>提交前挂钩，而无需沙盒

> [!NOTE] 
> 需要而无需沙盒挂钩[.NET Core](https://dotnet.github.io)系统路径中。

若要设置所包含的挂钩，打开 Windows/Linux/MacOS 控制台并执行：

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
使用此配置，签入文件时，Git 会验证应用程序通过所有测试，在使用安装在您的操作系统的工具在工作站中运行生成和测试。

项目代码样式的指南：

- 在合适，行长度被限制为 80 个字符最大帮助代码评审和命令行编辑器。

- 代码块缩进四个空格。 应避免使用选项卡 char。

- 文本文件使用 Unix 末尾行格式 (LF)。

- 使用管理依赖关系注入[Autofac](https://autofac.org)。

- Web 服务 Api 字段都 CamelCased 元数据除外。

## <a name="next-steps"></a>后续步骤

现在，已了解如何部署从零开始的 OPC 保管库，下面是建议的下一步：

> [!div class="nextstepaction"]
> [从零开始部署 OPC 孪生](howto-opc-twin-deploy-modules.md)