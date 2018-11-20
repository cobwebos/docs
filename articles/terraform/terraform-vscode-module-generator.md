---
title: 使用 Yeoman 在 Azure 中创建 Terraform 基模板
description: 了解如何使用 Yeoman 在 Azure 中创建 Terraform 基模板。
services: terraform
ms.service: terraform
keywords: terraform, devops, 虚拟机, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 9ef27166e84192dec81fd8f8da508785342ffefc
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288010"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>使用 Yeoman 在 Azure 中创建 Terraform 基模板

可以通过 [Terraform](https://docs.microsoft.com/azure/terraform/
) 轻松地在 Azure 上创建基础结构。 [Yeoman](http://yeoman.io/) 提供卓越的最佳做法框架，大大简化了模块开发人员创建 Terraform 模块的工作。

本文介绍如何使用 Yeoman 模块生成器来创建 Terraform 基模板。 然后会介绍如何使用两种不同的方法测试新的 Terraform 模板：

- 使用在本文中创建的 Docker 文件运行 Terraform 模块。
- 在 Azure Cloud Shell 中以本机方式运行 Terraform 模块。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- **Visual Studio Code**：我们会使用 [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) 来检查 Yeoman 生成器创建的文件。 但是，可以自行选择使用任何代码编辑器。
- **Terraform**：需安装 [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) 才能运行 Yeoman 创建的模块。
- **Docker**：我们将使用 [Docker](https://www.docker.com/get-started) 来运行 Yeoman 生成器创建的模块。 （如果你愿意，可以使用 Ruby 代替 Docker 来运行示例模块。）
- **Go 编程语言**：需安装 [Go](https://golang.org/)，因为由 Yeoman 生成的测试用例是以 Go 编写的。

>[!NOTE]
>本教程中的大多数过程涉及命令行条目。 此处介绍的步骤适用于所有操作系统和命令行工具。 在示例中，我们已选择对本地环境使用 PowerShell，对 Cloud Shell 环境使用 Git Bash。

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
1. 输入 `mkdir <new-directory-name>` 。

    >[!NOTE]
    >将 <new-directory-name> 替换为新目录的名称。 在此示例中，我们已将新目录命名为 `GeneratorDocSample`。

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 键入 `cd <new directory's name>`，然后按 **Enter**，以便导航到新目录中。

    ![导航到新目录](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >若要确保该目录为空，请输入 `ls`。 此命令生成的输出中应该没有文件列出。

## <a name="create-a-base-module-template"></a>创建基模块模板

在命令提示符下：

1. 输入 `yo az-terra-module` 。

1. 按照屏幕说明提供以下信息：

    - Terraform 模块项目名称

        ![项目名称](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >在此示例中，我们输入了 `doc-sample-module`。

    - 是否要包括 Docker 映像文件？

        ![包括 Docker 映像文件？](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >输入 `y` 。 如果选择 **n**，则生成的模块代码会支持仅以本机模式运行。

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

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>使用 Docker 文件测试新的 Terraform 模块

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

1. 输入 `docker run -it terra-mod-example /bin/sh` 。

    你现在正在 Docker 中运行，可以通过输入 `ls` 来列出文件。

    ![列出 Docker 文件](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>生成模块

1. 输入 `bundle install` 。

    等待“捆绑包完成”消息，然后继续下一步。

1. 输入 `rake build` 。

    ![Rake 生成](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>运行端到端测试

1. 输入 `rake e2e` 。

1. 片刻后，会显示“通过”消息。

    ![通过](media/terraform-vscode-module-generator/ymg-pass.png)

1. 输入 `exit`，完成端到端测试，然后退出 Docker 环境。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>使用 Yeoman 生成器在 Cloud Shell 中创建并测试模块

上一部分介绍了如何使用 Docker 文件测试 Terraform 模块。 此部分将使用 Yeoman 生成器在 Cloud Shell 中创建并测试模块。

使用 Cloud Shell 而不是 Docker 文件大大简化了过程。 使用 Cloud Shell：

- 无需安装 Node.js
- 无需安装 Yeoman
- 无需安装 Terraform

所有这些项都已预安装在 Cloud Shell 中。

### <a name="start-a-cloud-shell-session"></a>启动 Cloud Shell 会话

1. 通过 [Azure 门户](https:/portal.azure.com/)、[shell.azure.com](https://shell.azure.com) 或 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)启动 Azure Cloud Shell 会话。

1. 此时会打开“欢迎使用 Azure Cloud Shell”页面。 选择“Bash (Linux)”。 （不支持 Power Shell。）

    ![欢迎使用 Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >在此示例中，选择了 Bash (Linux)。

1. 如果尚未设置 Azure 存储帐户，则显示以下屏幕。 选择“创建存储”。

    ![尚未装载任何存储](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 在你之前所选的 shell 中启动，并显示其刚才创建的云驱动器的相关信息。

    ![已创建云驱动器](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>准备用于保存 Terraform 模块的文件夹

1. 此时，Cloud Shell 已经在环境变量中配置了 GOPATH。 若要查看路径，请输入 `go env`。

1. 创建 $GOPATH 文件夹（如果尚不存在）：输入 `mkdir ~/go`。

1. 在 $GOPATH 文件夹中创建一个文件夹：输入 `mkdir ~/go/src`。 该文件夹将用于保存和组织你可能会创建的不同的项目文件夹，例如在下一步创建的 <your-module-name> 文件夹。

1. 创建用于保存 Terraform 模块的文件夹：输入 `mkdir ~/go/src/<your-module-name>`。

    >[!NOTE]
    >在此示例中，我们选择了 `my-module-name` 作为文件夹名称。

1. 导航到模块文件夹：输入 `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>创建并测试 Terraform 模块

1. 输入 `yo az-terra-module`，按照向导中的说明操作。

    >[!NOTE]
    >当系统询问是否要创建 Docker 文件时，可以输入 `N`。

1. 输入 `bundle install`，安装依赖项。

    等待“捆绑包完成”消息，然后继续下一步。

1. 输入 `rake build`，生成模块。

    ![Rake 生成](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 输入 `rake e2e`，运行端到端测试。

1. 片刻后，会显示“通过”消息。

    ![通过](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装并使用 Azure Terraform Visual Studio Code 扩展。](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
