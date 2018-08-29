---
title: Azure Terraform VS Code 扩展 | Microsoft Docs
description: 本文将介绍如何在 Visual Studio Code 中安装和使用 Terraform 扩展。
keywords: terraform, devops, 虚拟机, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190710"
---
# <a name="azure-terraform-vs-code-extension"></a>Azure Terraform VS Code 扩展

Microsoft Azure Terraform Visual Studio Code (VS Code) 扩展旨在让开发人员在创作、测试 Terraform 和结合 Azure 使用它时更加高效。 VS Code 中的扩展提供了 Terraform 命令支持、资源图可视化和 CloudShell 集成。

## <a name="what-you-do"></a>准备工作

- 在计算机上安装开源 HashiCorp Terraform 可执行文件。
- 在本地 VS Code 安装项上安装 Terraform VS Code Extension for Azure。

## <a name="what-you-learn"></a>学习内容

在本教程中，学习以下内容：

- Terraform 如何自动执行和简化 Azure 服务的预配。
- 如何安装和使用 Azure 服务的 Microsoft Terraform VS Code 扩展。
- 如何使用 VS Code 编写、规划和执行 Terraform 计划。

## <a name="what-you-need"></a>所需条件

- 运行 Windows 10、Linux、macOS 10.10 或更高版本的计算机。
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US)。
- 一个有效的 Azure 订阅。 [激活 30 天免费试用版 Microsoft Azure 帐户](https://azure.microsoft.com/free/)。
- 在本地计算机上安装 [Terraform](https://www.terraform.io/) 开源工具。
  
## <a name="prepare-your-dev-environment"></a>准备开发环境

### <a name="install-git"></a>安装 Git

要完成本文中的练习，需要[安装 Git](https://git-scm.com/)。

### <a name="install-hashicorp-terraform"></a>安装 HashiCorp Terraform

按照 HashiCorp [Install Terraform](https://www.terraform.io/intro/getting-started/install.html)（安装 Terraform）网页上的说明进行操作，包括：

- 下载 Terraform
- 安装 Terraform
- 验证 Terraform 是否安装正确

>[!Tip]
>请务必按照 PATH 系统变量的相关设置说明进行操作。

### <a name="install-nodejs"></a>安装 Node.js

要在 Cloud Shell 中使用 Terraform，需要[安装 Node.js](https://nodejs.org/) 6.0 或更高版本。

>[!NOTE]
>要验证是否已安装 Node.js，请打开终端窗口并键入：`node -v`

### <a name="install-graphviz"></a>安装 GraphViz

要使用 Terraform 可视化函数，需要[安装 GraphViz](http://graphviz.org/)。

>[!NOTE]
>要验证是否已安装 GraphViz，请打开终端窗口并键入：`dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>安装 Azure Terraform VS Code 扩展：

1. 启动 VS Code。
2. 单击“扩展”图标。

    ![“扩展”按钮](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. 使用“在市场中搜索扩展”文本框搜索 Azure Terraform 扩展：

    ![在市场中搜索 VS Code 扩展](media/terraform-vscode-extension/tf-search-extensions.png)

4. 单击“安装”。

    >[!NOTE]
    >单击“安装”以安装 Azure Terraform 扩展时，VS Code 将自动安装 Azure 帐户扩展。 Azure 帐户是 Azure Terraform 扩展的依赖项文件，用于执行 Azure 订阅身份验证以及与 Azure 相关的代码扩展。

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>验证 Visual Studio Code 中是否已安装 Terraform 扩展

1. 单击“扩展”图标。
2. 在搜索文本框中键入 `@installed`。

    ![已安装的扩展](media/terraform-vscode-extension/tf-installed-extensions.png)

已安装的扩展列表中随即显示 Azure Terraform 扩展。

![已安装的 Terraform 扩展](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

现即可在 VS Code 内的 Cloud Shell 环境中运行所有受支持的 Terraform 命令。

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>练习 1：Terraform 命令基本演练

在本练习中，你会创建和执行一个基本的 Terraform 配置文件，它将预配一个新的 Azure 资源组。

### <a name="prepare-a-test-plan-file"></a>准备测试计划文件

1. 在 VS Code 中，从菜单栏中选择“文件”>“新建文件”。
2. 导航到 [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#)，并复制**用法示例**代码块中的代码：
3. 将复制的代码粘贴到在 VS Code 中创建的新文件中。

    ![粘贴“用法示例”代码](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >可更改资源组的名称值，但它必须对 Azure 订阅唯一。

4. 从菜单栏中选择“文件”>“另存为”。
5. 在“另存为”对话框中，导航到所选位置并单击“新建文件夹”。 （将新文件夹的名称更改为比“新建文件夹”更具描述性的名称。）

    >[!NOTE]
    >在本例中，文件夹名为 TERRAFORM-TEST-PLAN。

6. 请确保突出显示（选中）新文件夹，然后单击“打开”。
7. 在“另存为”对话框中，将文件的默认名称更改为 main.tf。

    ![另存为 main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. 单击“保存”。
- 从菜单栏中，选择“文件”>“打开文件夹”。 导航到并选择所创建的新文件夹。

### <a name="run-terraform-init-command"></a>运行 Terraform init 命令

1. 启动 Visual Studio Code。
2. 从 VS Code 菜单栏中，选择“文件”>“打开文件夹...”，再找到并选择 main.tf 文件。

    ![main.tf 文件](media/terraform-vscode-extension/tf-main-tf.png)

3. 从菜单栏中，选择“视图”>“命令面板...”>“Azure Terraform: Init”。
4. 几分钟后，当系统询问“是否要打开 Cloud Shell?”时 单击“确定”。

    ![是否要打开 Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. 首次从新文件夹启动 Cloud Shell 时，需要设置 Web 应用程序。 单击“打开”。

    ![首次启动 Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. 欢迎使用 Azure Cloud Shell。 选择 Bash 或 PowerShell。

    ![欢迎使用 Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >在此示例中，选择了 Bash (Linux)。

7. 如果尚未设置 Azure 存储帐户，则显示以下屏幕。 单击“创建存储”。

    ![尚未装载任何存储](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 在你之前所选的 shell 中启动，并显示其刚才创建的云驱动器的相关信息。

    ![已创建云驱动器](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. 现可退出 Cloud Shell
10. 从菜单栏中，选择“视图” > “命令面板” > “Azure Terraform: init”。

    ![已成功初始化 Terraform](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>直观呈现计划

在本教程的前面部分，你已安装 GraphViz。 Terraform 可使用 GraphViz 来生成配置或执行计划的视觉对象表示形式。 Azure Terraform VS Code 扩展通过 visualize 命令实现此功能。

- 从菜单栏中，选择“视图”>“命令面板...”>“Azure Terraform: 可视化”。

    ![直观呈现计划](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>运行 Terraform plan 命令

Terraform plan 命令用于检查一组更改的执行计划是否按预期执行。

>[!NOTE]
>Terraform plan 不对实际的 Azure 资源进行任何更改。 为了实际进行计划中所述的更改，我们使用 Terraform apply 命令。

- 从菜单栏中，选择“视图” > “命令面板” > “Azure Terraform: 计划”。

    ![Terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>运行 Terraform apply 命令

如果对 Terraform plan 命令的计划感到满意，则可运行 apply 命令。

1. 从菜单栏中，选择“视图” > “命令面板” > “Azure Terraform: 应用”。

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

2. 键入“是”。

    ![Terraform apply 是](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>验证是否已执行 Terraform 计划

要查看是否已成功创建新的 Azure 资源组，请执行以下操作：

1. 打开 Azure 门户。
2. 在左侧导航窗格中，选择“资源组”。

    ![验证新的资源](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

新的资源组应列入 NAME 列。

>[!NOTE]
>暂时将 Azure 门户窗口保持在打开状态；我们将在下一步中用到它。

### <a name="run-terraform-destroy-command"></a>运行 Terraform destroy 命令

1. 从菜单栏中，选择“视图” > “命令面板” > “Azure Terraform: 销毁”。

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. 键入“是”。

    ![Terraform destroy 是](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>验证是否已销毁资源组

要确认 Terraform 已成功销毁新的资源组，请执行以下操作：

1. 在 Azure 门户的资源组页面上单击“刷新”。
2. 资源组将不再列出。

    ![验证是否已销毁资源组](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>练习 2：Terraform 计算模块

在本练习中，了解如何将 Terraform 计算模块加载到 VS Code 环境中。

### <a name="clone-the-terraform-azurerm-compute-module"></a>克隆 terraform-azurerm-compute 模块

1. 请通过[此链接](https://github.com/Azure/terraform-azurerm-compute)访问 GitHub 上的 Terraform Azure Rm 计算模块。
2. 单击“克隆或下载”。

    ![克隆或下载](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >在本例中，我们的文件夹名为 terraform-azurerm-compute。

### <a name="open-the-folder-in-vs-code"></a>在 VS Code 中打开文件夹

1. 启动 Visual Studio Code。
2. 从“菜单栏”，选择“文件”>“打开文件夹”，然后导航到并选择在上一步骤中创建的文件夹。

    ![terraform-azurerm-compute 文件夹](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>初始化 Terraform

在开始从 VS Code 中使用 Terraform 命令之前，要下载 random 和 azurerm 这两个 Azure 提供程序的插件。

1. 在 VS Code IDE 的终端窗格中，键入 `terraform init`

    ![Terraform init 命令](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. 键入 `az login` 并按照屏幕上的说明操作。

### <a name="module-test-lint"></a>模块测试：lint

1. 从“菜单栏”中，选择“视图”>“命令面板...”>“Azure Terraform: 执行测试”。
2. 从测试类型选项列表中选择“lint”。

    ![选择测试类型](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. 当系统询问“是否要打开 CloudShell?”时，请单击“确定”并按照屏幕上的说明操作。

    ![是否要打开 CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>执行“lint”或“端到端”测试时，Azure 使用容器服务来预配用于执行实际测试的测试计算机。 因此，测试结果通常需要几分钟时间才能返回。

几分钟后，终端窗格中会显示与以下示例类似的列表：

![Lint 测试结果](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>模块测试：端到端

1. 从“菜单栏”中，选择“视图”>“命令面板...”>“Azure Terraform: 执行测试”。
2. 从测试类型选项列表中选择“端到端”。

    ![选择测试类型](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. 如果系统询问“是否要打开 CloudShell?”，请单击“确定”并按照屏幕上的说明操作。

    ![是否要打开 CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>执行“lint”或“端到端”测试时，Azure 使用容器服务来预配用于执行实际测试的测试计算机。 因此，测试结果通常需要几分钟时间才能返回。

几分钟后，终端窗格中会显示与以下示例类似的列表：

![端到端试结果](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>后续步骤

你已了解了 Terraform 可简化从 Visual Studio Code 中预配 Azure 服务的一些方法。 现在，你可能想要查看下述资源之一：
- [Terraform 模块注册表](https://registry.terraform.io/)列出了可供 Azure 及其他受支持的提供程序使用的所有 Terraform 模块。

对于其中的每个模块，提供了以下信息：

- 模块的常规功能及其特性的说明
- 用法示例
- 测试配置，它展示了如何在本地开发计算机上生成、运行和测试每个模块
- Dockerfile，它可用于在本地生成和运行模块开发环境。
