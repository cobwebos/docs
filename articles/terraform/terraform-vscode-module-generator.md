---
title: 教程 - 使用 Yeoman 在 Azure 中创建 Terraform 基模板
description: 了解如何使用 Yeoman 在 Azure 中创建 Terraform 基模板。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472139"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>教程：使用 Yeoman 在 Azure 中创建 Terraform 基模板

在本教程中，你将了解如何使用 [Terraform](/azure/terraform/) 和 [Yeoman](https://yeoman.io/) 的组合。 Terraform 是一种用于在 Azure 上创建基础结构的工具。 通过 Yeoman 可以轻松创建 Terraform 模块。

本文介绍如何执行以下任务：
> [!div class="checklist"]
> * 使用 Yeoman 模块生成器创建基本 Terraform 模板。
> * 使用两种不同的方法测试 Terraform 模板。
> * 使用 Docker 文件运行 Terraform 模块。
> * 在 Azure Cloud Shell 中以本机方式运行 Terraform 模块。

## <a name="prerequisites"></a>必备条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- **Visual Studio Code**：针对平台[下载 Visual Studio Code](https://code.visualstudio.com/download)。
- **Terraform**：[安装 Terraform](terraform-install-configure.md) 以运行 Yeoman 创建的模块。
- **Docker**：[安装 Docker](https://www.docker.com/get-started) 以运行 Yeoman 生成器创建的模块。
- **Go 编程语言**：[安装 Go](https://golang.org/)，因为 Yeoman 生成的测试用例是使用 Go 语言的代码。

>[!NOTE]
>本教程中的大多数过程涉及命令行接口。 介绍的步骤适用于所有操作系统和命令行工具。 对于这些示例，已为本地环境选择 PowerShell，为 cloud shell 环境选择 Git Bash。

## <a name="prepare-your-environment"></a>准备环境

### <a name="install-nodejs"></a>安装 Node.js

要在 Cloud Shell 中使用 Terraform，需要[安装 Node.js](https://nodejs.org/en/download/) 6.0 或更高版本。

>[!NOTE]
>若要验证是否已安装 Node.js，请打开终端窗口并输入 `node --version`。

### <a name="install-yeoman"></a>安装 Yeoman

运行以下命令：

```bash
npm install -g yo
```

![安装 Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>安装适用于 Terraform 模块的 Yeoman 模板

运行以下命令：

```bash
npm install -g generator-az-terra-module
```

![安装 generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

若要验证是否已安装 Yeoman，请运行以下命令：

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>为 Yeoman 生成的模块创建目录

Yeoman 模板在当前目录中生成文件。 由于此原因，需创建一个目录。

此空目录需置于 $GOPATH/src 下。 有关此路径的详细信息，请参阅[设置 GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 一文。

1. 导航到要从中创建新目录的父目录。

1. 运行以下命令替换占位符。 在此示例中，使用 `GeneratorDocSample` 目录名。

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 导航到新目录：

    ```bash
    cd <new-directory-name>
    ```

    ![导航到新目录](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>创建基模块模板

1. 运行以下命令：

    ```bash
    yo az-terra-module
    ```

1. 按照屏幕说明提供以下信息：

    - **Terraform 模块项目名** - 本例使用了 `doc-sample-module` 的值。

        ![项目名称](media/terraform-vscode-module-generator/ymg-project-name.png)       


    -  是否要包括 Docker 映像文件？ - 输入 `y`。 如果选择 `n`，则生成的模块代码会支持仅以本机模式运行。

        ![包括 Docker 映像文件？](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. 列出目录内容，以查看创建的结果文件：

    ```bash
    ls
    ```

    ![列出创建的文件](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>查看生成的模块代码

1. 启动 Visual Studio Code

1. 在菜单栏中选择“文件”>“打开文件夹”，然后选择已创建的文件夹  。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

以下文件是由 Yeoman 模块生成器创建的。 有关这些文件及其用法的详细信息，请参阅 [Terratest in Terraform Modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)（Terraform 模块中的 Terratest）。

- `main.tf` - 定义名为 `random-shuffle` 的模块。 输入是一个 `string_list`。 输出是排列的计数。
- `variables.tf` - 定义模块所用的输入和输出变量。
- `outputs.tf` - 定义模块的输出内容。 此处为内置的 Terraform 模块 `random_shuffle` 返回的值。
- `Rakefile` - 定义生成步骤。 这些步骤包括：
    - `build` - 验证 main.tf 文件的格式设置。
    - `unit` - 生成的模块框架不包括单元测试的代码。 如需指定单元测试方案，则请在此处添加该代码。
    - `e2e` - 运行模块的端到端测试。
- `test`
    - 测试用例以 Go 编写。
    - 测试中的所有代码均为端到端测试。
    - 端到端测试尝试预配 `fixture` 下定义的所有项。 将 `template_output.go` 文件中的结果与预定义的期望值进行比较。
    - `Gopkg.lock` 和 `Gopkg.toml`：定义依赖项。 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>使用 Docker 文件测试新的 Terraform 模块

本部分介绍如何使用 Docker 文件测试 Terraform 模块。

>[!NOTE]
>本示例在本地运行模块；不在 Azure 上运行。

### <a name="confirm-docker-is-installed-and-running"></a>确认 Docker 已安装并运行

从命令提示符输入 `docker version`。

![Docker 版本](media/terraform-vscode-module-generator/ymg-docker-version.png)

生成的输出确认 Docker 已安装。

若要确认 Docker 是否确实在运行，请输入 `docker info`。

![Docker 信息](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>设置 Docker 容器

1. 从命令提示符输入

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .` 列中的一个值匹配。

    将会显示“已成功生成”消息。 

    ![指示成功生成的消息](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. 在命令提示符下，输入 `docker image ls` 以查看列出的已创建模块 `terra-mod-example`。

    ![包含新模块的列表](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. 输入 `docker run -it terra-mod-example /bin/sh`。 运行 `docker run` 命令后，就进入了 Docker 环境。 此时可以使用 `ls` 命令发现该文件。

    ![Docker 中的文件列表](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>生成模块

1. 运行以下命令：

    ```bash
    bundle install
    ```

1. 运行以下命令：

    ```bash
    rake build
    ```

    ![Rake 生成](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>运行端到端测试

1. 运行以下命令：

    ```bash
    rake e2e
    ```

1. 片刻后，会显示“通过”消息。 

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. 输入 `exit` 完成该测试，然后退出 Docker 环境。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>使用 Yeoman 生成器在 Cloud Shell 中创建并测试模块

此部分将使用 Yeoman 生成器在 Cloud Shell 中创建并测试模块。 使用 Cloud Shell 而不是 Docker 文件大大简化了过程。 使用 Cloud Shell，将预先安装以下产品：

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>启动 Cloud Shell 会话

1. 通过 [Azure 门户](https://portal.azure.com/)、[shell.azure.com](https://shell.azure.com) 或 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)启动 Azure Cloud Shell 会话。

1. 此时会打开“欢迎使用 Azure Cloud Shell”页面。  选择“Bash (Linux)”。 

    ![欢迎使用 Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. 如果尚未设置 Azure 存储帐户，则显示以下屏幕。 选择“创建存储”。 

    ![尚未装载任何存储](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 在你之前所选的 shell 中启动，并显示其刚才创建的云驱动器的相关信息。

    ![已创建云驱动器](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>准备用于保存 Terraform 模块的目录

1. 此时，Cloud Shell 已经在环境变量中配置了 GOPATH。 若要查看路径，请输入 `go env`。

1. 创建 $GOPATH 目录（如果尚不存在）：输入 `mkdir ~/go`。

1. 在 $GOPATH 目录中创建目录。 此目录用于保存在此示例中创建的不同项目目录。 

    ```bash
    mkdir ~/go/src
    ```

1. 创建一个目录，用于保存替换占位符的 Terraform 模块。 在此示例中，使用 `my-module-name` 目录名。

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. 导航到模块目录： 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>创建并测试 Terraform 模块

1. 运行以下命令，然后按照说明进行操作。 当系统询问是否要创建 Docker 文件时，请输入 `N`。

    ```bash
    yo az-terra-module
    ```

1. 运行以下命令安装依赖项：

    ```bash
    bundle install
    ```

1. 运行以下命令生成模块：

    ```bash
    rake build
    ```

    ![Rake 生成](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 运行以下命令运行文本：

    ```bash
    rake e2e
    ```

    ![测试通过结果](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装并使用 Azure Terraform Visual Studio Code 扩展](/azure/terraform/terraform-vscode-extension)。