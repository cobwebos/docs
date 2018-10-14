---
title: Azure Terraform VS Code 模块生成器
description: 了解如何使用 Yeoman 创建 Terraform 基模板。
services: terraform
ms.service: terraform
keywords: terraform, devops, 虚拟机, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396172"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>使用 Yeoman 创建 Terraform 基模板

可以通过 [Terraform](https://docs.microsoft.com/azure/terraform/
) 轻松地在 Azure 上创建基础结构。 [Yeoman](http://yeoman.io/) 提供卓越的最佳做法框架，大大简化了模块开发人员创建 Terraform 模块的工作。

本文介绍如何使用 Yeoman 模块生成器来创建 Terraform 基模板。

## <a name="prerequisites"></a>先决条件

- 运行 Windows 10、Linux、macOS 10.10 或更高版本的计算机。
- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- **Visual Studio Code**：我们会使用 [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) 来检查 Yeoman 生成器创建的文件。 但是，可以自行选择使用任何代码编辑器。
- **Terraform**：需安装 [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) 才能运行 Yeoman 创建的模块。
- **Docker**：我们将使用 [Docker](https://www.docker.com/get-started) 来运行 Yeoman 生成器创建的模块。 （如果你愿意，可以使用 Ruby 代替 Docker 来运行示例模块。）
- **Go 编程语言**：需安装 [Go](https://golang.org/)，因为由 Yeoman 生成的测试用例是以 Go 编写的。

>[!NOTE]
>本教程中的大多数过程涉及命令行条目。 此处介绍的步骤适用于所有操作系统和命令行工具。 在示例中，我们已选择使用 PowerShell。 但是，你可以使用多个替代工具中的任意一个，例如 Git Bash、Windows 命令提示符，或者 Linux 或 macOS 命令行命令。

## <a name="prepare-your-environment"></a>准备环境

### <a name="install-nodejs"></a>安装 Node.js

要在 Cloud Shell 中使用 Terraform，需要[安装 Node.js](https://nodejs.org/en/download/) 6.0 或更高版本。

>[!NOTE]
>若要验证是否已安装 Node.js，请打开终端窗口并输入 `node --version`。

### <a name="install-yeoman"></a>安装 Yeoman

从命令提示符输入 `npm install -g yo`

![安装 Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>安装适用于 Terraform 模块的 Yeoman 模板

从命令提示符输入 `npm install -g generator-az-terra-module`。

![安装 generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>若要验证是否已安装 Yeoman，请从终端窗口输入 `yo --version`。

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>创建一个空文件夹，用于保存 Yeoman 生成的模块

Yeoman 模板在**当前目录**中生成文件。 由于此原因，需创建一个目录。

>[!Note]
>此空目录需置于 $GOPATH/src 下。可以在[此处](https://github.com/golang/go/wiki/SettingGOPATH)找到完成此操作所需的说明。

在命令提示符下：

1. 导航到父目录，需在其中包含一个新的空目录（尚未创建）。
1. 输入 `mkdir <new-directory-name>`。

    >[!NOTE]
    >将 <new-directory-name> 替换为新目录的名称。 在此示例中，我们已将新目录命名为 `GeneratorDocSample`。

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 键入 `cd <new directory's name>`，然后按 **Enter**，以便导航到新目录中。

    ![导航到新目录](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >若要确保该目录为空，请输入 `ls`。 此命令生成的输出中应该没有文件列出。

## <a name="create-a-base-module-template"></a>创建基模块模板

在命令提示符下：

1. 输入 `yo az-terra-module`。

1. 按照屏幕说明提供以下信息：

    - Terraform 模块项目名称

        ![项目名称](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >在此示例中，我们输入了 `doc-sample-module`。

    - 是否要包括 Docker 映像文件？

        ![包括 Docker 映像文件？](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >输入 `y`。 如果选择 **n**，则生成的模块代码会支持仅以本机模式运行。

3. 输入 `ls` 查看以创建方式生成的文件。

    ![列出创建的文件](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>查看生成的模块代码

1. 启动 Visual Studio Code

1. 在菜单栏中选择“文件”>“打开文件夹”，然后选择已创建的文件夹。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

请看看通过 Yeoman 模块生成器创建的部分文件。

>[!Note]
>在本文中，我们将使用通过 Yeoman 模块生成器创建的 main.tf、variables.tf 和 outputs.tf 文件。 但是，在创建你自己的模块时，需根据 Terraform 模块的功能对这些文件进行编辑。 有关这些文件及其用法的更深入探讨，请参阅 [Terratest in Terraform Modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)（Terraform 模块中的 Terratest）。

### <a name="maintf"></a>main.tf

定义名为 *random-shuffle* 的模块。 输入为 *string_list*。 输出是排列的计数。

### <a name="variablestf"></a>variables.tf

定义模块所使用的输入和输出变量。

### <a name="outputstf"></a>outputs.tf

定义模块的输出。 在这里，它是内置的 Terraform 模块 **random_shuffle** 返回的值。

### <a name="rakefile"></a>Rakefile

定义生成步骤。 这些步骤包括：

- **build**：验证 main.tf 文件的格式。
- **unit**：生成的模块框架不包括单元测试的代码。 如需指定单元测试方案，则请在此处添加该代码。
- **e2e**：运行模块的端到端测试。

### <a name="test"></a>测试

- 测试用例以 Go 编写。
- 测试中的所有代码均为端到端测试。
- 端到端测试尝试使用 Terraform 来预配在 **fixture** 下定义的所有项，然后将 **template_output.go** 代码中的输出与与定义的预期值进行比较。
- **Gopkg.lock** 和 **Gopkg.toml**：定义依赖项。 

## <a name="test-the-module-using-docker"></a>使用 Docker 来测试模块

>[!NOTE]
>在示例中，我们将模块作为本地模块运行，实际上不接触 Azure。

### <a name="confirm-docker-is-installed-and-running"></a>确认 Docker 已安装并运行

从命令提示符输入 `docker version`。

![Docker 版本](media/terraform-vscode-module-generator/ymg-docker-version.png)

生成的输出确认 Docker 已安装。

若要确认 Docker 是否确实在运行，请输入 `docker info`。

![Docker 信息](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>设置 Docker 容器

1. 从命令提示符输入

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`。

    将会显示“已成功生成”消息。

    ![已成功生成](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. 从命令提示符输入 `docker image ls`。

    此时会看到新创建的模块 *terra-mod-example* 已列出。

    ![存储库结果](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >模块的名称 *terra-mod-example* 已在命令中指定，该命令是在上面的步骤 1 中输入的。

1. 输入 `docker run -it terra-mod-example /bin/sh`。

    你现在正在 Docker 中运行，可以通过输入 `ls` 来列出文件。

    ![列出 Docker 文件](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

1. 输入 `bundle install`。

    等待“捆绑包完成”消息，然后继续下一步。

1. 输入 `rake build`。

    ![Rake 生成](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>执行端到端测试

1. 输入 `rake e2e`。

1. 片刻后，会显示“通过”消息。

    ![通过](media/terraform-vscode-module-generator/ymg-pass.png)

1. 输入 `exit`，完成端到端测试。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装并使用 Azure Terraform Visual Studio Code 扩展。](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
