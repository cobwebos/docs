---
title: 使用 Azure Pipelines 进行持续集成
description: 了解如何持续构建、测试和部署 Azure 资源管理器模板。
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dacbdbcbebbbd696c14745e055ed9f7bd7905b1d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731928"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板

在[上一篇教程](./deployment-tutorial-linked-template.md)中，你部署了一个链接模板。  本教程介绍如何使用 Azure Pipelines 持续生成和部署 Azure 资源管理器模板项目。

Azure DevOps 提供开发人员服务，以支持团队规划工作、协作开发代码以及构建和部署应用程序。 通过使用 Azure DevOps Services，开发人员能够在云中工作。 Azure DevOps 提供了一组集成的功能，可以通过 Web 浏览器或 IDE 客户端访问这些功能。 Azure Pipelines 是这些功能中的一项。 Azure Pipelines 是一项别具特色的持续集成 (CI) 和持续交付 (CD) 服务。 它适用于你喜欢的 Git 提供程序，并且可以部署到大多数主要云服务。 然后，可以自动化构建和测试代码并将其部署到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services 。

> [!NOTE]
> 选择项目名称。 完成本教程后，请将任意“AzureRmPipeline”替换为自己的项目名称  。
> 此项目名称用于生成资源名称。  其中一个资源是存储帐户。 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。 该名称必须是唯一的。 在模板中，存储帐户名称是追加了“store”的项目名称，项目名称的长度必须介于 3 到 11 个字符之间。 因此，项目名称必须符合存储帐户名称要求，且短于 11 个字符。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 准备 GitHub 存储库
> * 创建 Azure DevOps 项目
> * 创建 Azure 管道
> * 验证管道部署
> * 更新模板并重新部署
> * 清理资源

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* **GitHub 帐户**，用于为模板创建存储库。 如果没有帐户，可以[免费创建一个帐户](https://github.com)。 有关使用 GitHub 存储库的详细信息，请参阅[构建 GitHub 存储库](/azure/devops/pipelines/repos/github)。
* **安装 Git**。 本教程说明使用 Git Bash 或 Git Shell   。 如需说明，请参阅[安装 Git]( https://www.atlassian.com/git/tutorials/install-git)。
* **Azure DevOps 组织**。 如果没有组织，可以免费创建一个组织。 请参阅[创建组织或项目集合]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)。
* （可选）**包含资源管理器工具扩展的 Visual Studio Code**。 请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](use-vs-code-to-create-template.md)。

## <a name="prepare-a-github-repository"></a>准备 GitHub 存储库

GitHub 用于存储项目源代码，包括资源管理器模板。 有关其他受支持的存储库，请参阅 [Azure DevOps 支持的存储库](/azure/devops/pipelines/repos/?view=azure-devops)。

### <a name="create-a-github-repository"></a>创建 GitHub 存储库

如果没有 GitHub 帐户，请参阅[先决条件](#prerequisites)。

1. 登录 [GitHub](https://github.com)。
1. 选择右上角的帐户图像，然后选择“你的存储库”  。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 创建 GitHub 存储库](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. 选择“新建”（一个绿色的按钮）  。
1. 在“存储库名称”中，输入存储库名称  。  例如，“AzureRmPipeline-repo”  。 请将任意“AzureRmPipeline”替换为自己的项目名称  。 可以选择“公共”或“专用”以完成本教程   。 然后，选择“创建存储库”  。
1. 记下 URL。 存储库 URL 的格式为：`https://github.com/[YourAccountName]/[YourRepositoryName]` 。

此存储库称为远程存储库  。 同一项目中的每个开发人员都可以克隆自己的本地存储库，并将更改合并到远程存储库  。

### <a name="clone-the-remote-repository"></a>克隆远程存储库

1. 打开 Git Shell 或 Git Bash。  请参阅[先决条件](#prerequisites)。
1. 确认当前文件夹是否为 **GitHub**。
1. 运行以下命令：

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    请将“[YourAccountName]”替换为自己的 GitHub 帐户名称，并将“[YourGitHubRepositoryName]”替换为在上述过程中创建的存储库名称   。

**CreateWebApp** 文件夹是存储模板的文件夹。 使用“pwd”命令可显示文件夹路径  。 在以下过程中，请将模板保存到该路径。

### <a name="download-a-quickstart-template"></a>下载快速入门模板

如果不创建模板的话，可以下载模板并将其保存到 **CreateWebApp** 文件夹。

* 主模板： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* 链接模板： https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

使用的文件夹名称和文件名与管道中的名称相同。  如果更改这些名称，则必须更新管道中使用的名称。

### <a name="push-the-template-to-the-remote-repository"></a>将模板推送到远程存储库

azuredeploy.json 已添加到本地存储库。 下一步，将模板上传到远程存储库。

1. 请打开 Git Shell 或 Git Bash（如果未打开）   。
1. 将目录更改为本地存储库中的 CreateWebApp 文件夹。
1. 验证 azuredeploy.json 文件是否位于该文件夹中  。
1. 运行以下命令：

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    可能会收到一条有关 LF 的警告。 可以忽略该警告。 “主干”表示主分支  。  通常，每次更新创建一个分支。 若要简化本教程，请直接使用主分支。
1. 从浏览器浏览到 GitHub 存储库。  该 URL 为 `https://github.com/[YourAccountName]/[YourGitHubRepository]` 。 应会看到 **CreateWebApp** 文件夹以及其中的三个文件。
1. 选择“linkedStorageAccount.json”打开模板。 
1. 选择“原始”按钮。  URL 以 **raw.githubusercontent.com** 开头。
1. 复制 URL。  稍后在本教程中配置管道时需要提供此值。

到目前为止，你已创建一个 GitHub 存储库，并已将模板上传到其中。

## <a name="create-a-devops-project"></a>创建 DevOps 项目

需要 DevOps 组织才能继续进行下一步。  如果没有组织，请参阅[先决条件](#prerequisites)。

1. 登录 [Azure DevOps](https://dev.azure.com)。
1. 从左侧选择一个 DevOps 组织。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 创建 Azure DevOps 项目](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. 选择“新建项目”。  如果没有任何项目，将自动打开“创建项目”页面。
1. 输入以下值：

    * **项目名称**：输入项目名称。 可以使用在本教程开头选择的项目名称。
    * **版本控制**：选择“Git”  。 可能需要展开“高级”才能看到“版本控制”   。

    可以使用其他属性的默认值。
1. 选择“创建”  。

创建服务连接，用于将项目部署到 Azure。

1. 从左侧菜单的底部选择“项目设置”  。
1. 在“管道”下，选择“服务连接”   。
1. 依次选择“新建服务连接”、“Azure 资源管理器”、“下一步”。   
1. 依次选择“服务主体”、“下一步”。  
1. 输入以下值：

    * **作用域级别**：选择“订阅”  。
    * **订阅**：选择自己的订阅。
    * **资源组**：将此字段留空。
    * **连接名称**：输入连接名称。 例如，“AzureRmPipeline-conn”  。 请记下此名称，创建管道时需要使用它。
    * **授予对所有管道的访问权限**。 (选定)
1. 选择“保存”。 

## <a name="create-a-pipeline"></a>创建管道

到目前为止，你已完成了以下任务。  如果由于能熟练使用 GitHub 和 DevOps 而跳过前面几节，则必须先完成这些任务，然后再继续。

* 创建 GitHub 存储库，并将模板保存到存储库中的 **CreateWebApp** 文件夹。
* 创建 DevOps 项目，并创建 Azure 资源管理器服务连接。

若要以包含部署模板步骤的方式创建管道，请执行以下操作：

1. 在左侧菜单中选择“管道”  。
1. 选择“新建管道”  。
1. 从“连接”选项卡中，选择“GitHub”   。 如果系统要求输入 GitHub 凭据，然后按照说明进行操作。 如果看到以下屏幕，请选择“仅选择存储库”，并验证存储库是否位于列表中，然后选择“批准和安装”   。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines“仅选择存储库”](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. 从“选择”选项卡中，选择自己的存储库  。  默认名称为“[YourAccountName]/[YourGitHubRepositoryName]”  。
1. 从“配置”选项卡中，选择“初级管道”   。 它显示了“azure-pipelines.yml”管道文件，其中包含两个脚本步骤  。
1. 从 yml 文件中删除两个脚本步骤。
1. 将光标移到 **steps:** 后面的行。
1. 在屏幕右侧选择“显示助手”打开“任务”窗格。  
1. 选择“ARM 模板部署”。 
1. 输入以下值：

    * **deploymentScope**：选择“资源组”。  若要详细了解范围，请参阅[部署范围](deploy-rest.md#deployment-scope)。
    * **Azure 资源管理器连接**：选择前面创建的服务连接名称。
    * **订阅**：指定目标订阅 ID。
    * **操作**：选择“创建或更新资源组”操作，执行 2 项操作 - 1.  如果提供了新的资源组名称，则创建资源组；2. 部署指定的模板。
    * **资源组**：输入新的资源组名称。 例如，“AzureRmPipeline-rg”  。
    * **位置**：选择资源组的位置，例如“美国中部”。 
    * **模板位置**：选择“链接的项目”，表示任务将直接从连接的存储库中查找模板文件。 
    * **模板**：输入 **CreateWebApp/azuredeploy.json**。 如果更改了文件夹名称和文件名，则需要更改此值。
    * **模板参数**：将此字段留空。 稍后将在 **“替代模板参数”中指定参数值。
    * **overrideParameters**：输入 **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]** 。 替换项目名称和链接模板 URL。 链接模板 URL 是在完成[创建 GitHub 存储库](#create-a-github-repository)部分时记下的 URL。
    * **部署模式**：选择“增量”。 
    * **部署名称**：输入 **DeployPipelineTemplate**。 选择“高级”，然后可以看到“部署名称”。  

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 步骤](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. 选择 **添加** 。

    有关任务的详细信息，请参阅 [Azure 资源组部署任务](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)和 [Azure 资源管理器模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    yml 文件应如下所示：

    ![Azure 资源管理器 Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. 选择“保存并运行”  。
1. 在“保存并运行”窗格中，再次选择“保存并运行”。   YAML 文件的副本将保存到已连接的存储库中。 浏览到存储库即可查看该 YAML 文件。
1. 验证管道是否成功执行。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开资源组。 名称是在管道 YAML 文件中指定的名称。  你将看到创建了一个存储帐户。  存储帐户名称以“存储”开头  。
1. 选择存储帐户名称以将其打开。
1. 选择“属性”  。 注意“复制”是“本地冗余存储(LRS)”   。

## <a name="update-and-redeploy"></a>更新并重新部署

更新模板并将更改推送到远程存储库时，管道会自动更新资源，在本例中即存储帐户。

1. 在 Visual Studio Code 或任何文本编辑器中，打开本地存储库中的 **linkedStorageAccount.json**。
1. 将“storageAccountType”的“defaultValue”更新为“Standard_GRS”    。 请参阅下面的屏幕截图：

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 更新 yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. 保存更改。
1. 从 Git Bash/Shell 运行以下命令，将更改推送到远程存储库。

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    第一个命令 (pull) 将本地存储库与远程存储库同步。 管道 YAML 文件只会添加到远程存储库。 运行 pull 命令会将 YAML 文件的副本下载到本地分支。

    第四个命令 (push) 将修改后的 linkedStorageAccount.json 文件上传到远程存储库。 更新远程存储库的主分支后，将再次发送管道。

若要验证所做的更改，可以检查存储帐户的“复制”属性。  请参阅[验证部署](#verify-the-deployment)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

你可能还需要删除 GitHub 存储库和 Azure DevOps 项目。

## <a name="next-steps"></a>后续步骤

祝贺你完成本资源管理器模板部署教程。 如果你有任何意见和建议，请在反馈部分告知我们。 谢谢！
你现在可以学习有关模板的更高级概念了。 下一教程会更详细地探讨如何根据模板参考文档来定义要部署的资源。

> [!div class="nextstepaction"]
> [利用模板参考](./template-tutorial-use-template-reference.md)
