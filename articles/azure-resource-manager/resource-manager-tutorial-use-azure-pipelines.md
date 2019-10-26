---
title: 使用 Azure Pipelines 进行持续集成 | Microsoft Docs
description: 了解如何持续构建、测试和部署 Azure 资源管理器模板。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b176e97a546335f597d4cf424d7feb4f5fa0f775
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597224"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板

了解如何使用 Azure Pipelines 持续构建和部署 Azure 资源管理器模板项目。

Azure DevOps 提供开发人员服务，以支持团队规划工作、协作开发代码以及构建和部署应用程序。 通过使用 Azure DevOps Services，开发人员能够在云中工作。 Azure DevOps 提供了一组集成的功能，可以通过 Web 浏览器或 IDE 客户端访问这些功能。 Azure Pipelines 是这些功能中的一项。 Azure Pipelines 是一项别具特色的持续集成 (CI) 和持续交付 (CD) 服务。 它适用于你喜欢的 Git 提供程序，并且可以部署到大多数主要云服务。 然后，可以自动化构建和测试代码并将其部署到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services 。

本教程专为刚开始使用 Azure DevOps Services 和 Azure Pipelines 的 Azure 资源管理器模板开发人员设计。 如果你已能熟练使用 GitHub 和 DevOps，则可以跳到[创建管道](#create-a-pipeline)。

> [!NOTE]
> 选择项目名称。 完成本教程后，请将任意“AzureRmPipeline”替换为自己的项目名称  。

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
* **包含资源管理器工具扩展的 [Visual Studio Code](https://code.visualstudio.com/)** 。 请参阅[安装扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。

## <a name="prepare-a-github-repository"></a>准备 GitHub 存储库

GitHub 用于存储项目源代码，包括资源管理器模板。 有关其他受支持的存储库，请参阅 [Azure DevOps 支持的存储库](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)。

### <a name="create-a-github-repository"></a>创建 GitHub 存储库

如果没有 GitHub 帐户，请参阅[先决条件](#prerequisites)。

1. 登录 [GitHub](https://github.com)。
2. 选择右上角的帐户图像，然后选择“你的存储库”  。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 创建 GitHub 存储库](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. 选择“新建”（一个绿色的按钮）  。
1. 在“存储库名称”中，输入存储库名称  。  例如，“AzureRmPipeline-repo”  。 请将任意“AzureRmPipeline”替换为自己的项目名称  。 可以选择“公共”或“专用”以完成本教程   。 然后，选择“创建存储库”  。
1. 记下 URL。 存储库 URL 格式如下：

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

此存储库称为远程存储库  。 同一项目中的每个开发人员都可以克隆自己的本地存储库，并将更改合并到远程存储库  。

### <a name="clone-the-remote-repository"></a>克隆远程存储库

1. 打开 Git Shell 或 Git Bash。  请参阅[先决条件](#prerequisites)。
1. 验证当前文件夹是否为“github”  。
1. 运行以下命令：

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    请将“[YourAccountName]”替换为自己的 GitHub 帐户名称，并将“[YourGitHubRepositoryName]”替换为在上述过程中创建的存储库名称   。

    以下屏幕截图显示了示例。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 创建 GitHub Bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

“CreateAzureStorage”文件夹是存储模板的文件夹  。 使用“pwd”命令可显示文件夹路径  。 在以下过程中，请将模板保存到该路径。

### <a name="download-a-quickstart-template"></a>下载快速入门模板

可以下载[快速入门模板]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)，而不必创建模板。 此模板创建 Azure 存储帐户。

1. 打开 Visual Studio 代码。 请参阅[先决条件](#prerequisites)。
2. 使用以下 URL 打开模板：

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. 将文件另存为“azuredeploy.json”并保存到“CreateAzureStorage”文件夹   。 在管道中按原样使用文件夹名称和文件名。  如果更改这些名称，则必须更新管道中使用的名称。

### <a name="push-the-template-to-the-remote-repository"></a>将模板推送到远程存储库

azuredeploy.json 已添加到本地存储库。 下一步，将模板上传到远程存储库。

1. 请打开 Git Shell 或 Git Bash（如果未打开）   。
1. 将目录更改为本地存储库中的 CreateAzureStorage 文件夹。
1. 验证 azuredeploy.json 文件是否位于该文件夹中  。
1. 运行以下命令：

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    可能会收到一条有关 LF 的警告。 可以忽略该警告。 “主干”表示主分支  。  通常，每次更新创建一个分支。 若要简化本教程，请直接使用主分支。
1. 从浏览器浏览到 GitHub 存储库。  URL 为 https://github.com/ [YourAccountName]/[YourGitHubRepository]  。 应会看到“CreateAzureStorage”文件夹以及该文件夹中的“Azuredeploy.json”文件   。

到目前为止，你已经创建了一个 GitHub 存储库并向其中上传模板。

## <a name="create-a-devops-project"></a>创建 DevOps 项目

需要 DevOps 组织才能继续进行下一步。  如果你没有组织，请参阅[先决条件](#prerequisites)。

1. 登录 [Azure DevOps](https://dev.azure.com)。
1. 从左侧选择一个 DevOps 组织。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 创建 Azure DevOps 项目](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. 选择“创建项目”。  如果没有任何项目，将自动打开“创建项目”页面。
1. 输入以下值：

    * **项目名称**：输入项目名称。 可以使用在本教程开头选择的项目名称。
    * **版本控制**：选择“Git”  。 可能需要展开“高级”才能看到“版本控制”   。

    可以使用其他属性的默认值。
1. 选择“创建项目”。 

创建服务连接，用于将项目部署到 Azure。

1. 从左侧菜单的底部选择“项目设置”  。
1. 在“管道”下，选择“服务连接”   。
1. 选择“新建服务连接”，然后选择“AzureResourceManager”   。
1. 输入以下值：

    * **连接名称**：输入连接名称。 例如，“AzureRmPipeline-conn”  。 请记下此名称，创建管道时需要使用它。
    * **作用域级别**：选择“订阅”  。
    * **订阅**：选择自己的订阅。
    * **资源组**：将此字段留空。
    * 允许所有管道使用此连接。  (选定)
1. 选择“确定”  。

## <a name="create-a-pipeline"></a>创建管道

到目前为止，你已完成了以下任务。  如果由于能熟练使用 GitHub 和 DevOps 而跳过前面几节，则必须先完成这些任务，然后再继续。

- 创建 GitHub 存储库，并将[此模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)保存到存储库中的“CreateAzureStorage”文件夹  。
- 创建 DevOps 项目，并创建 Azure 资源管理器服务连接。

若要以包含部署模板步骤的方式创建管道，请执行以下操作：

1. 在左侧菜单中选择“管道”  。
1. 选择“新建管道”  。
1. 从“连接”选项卡中，选择“GitHub”   。 如果系统要求输入 GitHub 凭据，然后按照说明进行操作。 如果看到以下屏幕，请选择“仅选择存储库”，并验证存储库是否位于列表中，然后选择“批准和安装”   。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines“仅选择存储库”](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. 从“选择”选项卡中，选择自己的存储库  。  默认名称为“[YourAccountName]/[YourGitHubRepositoryName]”  。
1. 从“配置”选项卡中，选择“初级管道”   。 它显示了“azure-pipelines.yml”管道文件，其中包含两个脚本步骤  。
1. 将“steps”部分替换为以下 YAML  ：

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    应如下所示：

    ![Azure 资源管理器 Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    进行以下更改：

    * **deloymentScope**：从以下选项中选择部署范围：`Management Group`、`Subscription` 和 `Resource Group`。 在本教程中使用**资源组**。 若要详细了解范围，请参阅[部署范围](./resource-group-template-deploy-rest.md#deployment-scope)。
    * **ConnectedServiceName**：指定前面创建的服务连接名称。
    * **SubscriptionName**：指定目标订阅 ID。
    * **操作**：“创建或更新资源组”操作执行 2 项操作 - 1.  如果提供了新的资源组名称，则创建资源组；2. 部署指定的模板。
    * **resourceGroupName**：指定新的资源组名称。 例如，“AzureRmPipeline-rg”  。
    * **位置**：指定资源组的位置。
    * **templateLocation**：指定“链接的项目”时，任务直接从连接的存储库中查找模板文件  。
    * **csmFile** 为模板文件的路径。 无需指定模板参数文件，因为模板中定义的所有参数都具有默认值。

    有关任务的详细信息，请参阅 [Azure 资源组部署任务](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)和 [Azure 资源管理器模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. 选择“保存并运行”  。
1. 再次选择“保存并运行”  。 YAML 文件的副本将保存到已连接的存储库中。 浏览到存储库即可查看该 YAML 文件。
1. 验证管道是否成功执行。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>验证部署

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 打开资源组。 名称是在管道 YAML 文件中指定的名称。  你将看到创建了一个存储帐户。  存储帐户名称以“存储”开头  。
1. 选择存储帐户名称以将其打开。
1. 选择“属性”  。 请注意，“SKU”是“Standard_LRS”   。

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 门户验证](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>更新并重新部署

更新模板并将更改推送到远程存储库时，管道会自动更新资源，在本例中即存储帐户。

1. 在 Visual Studio Code 中从本地存储库中打开“azuredeploy.json”  。
1. 将“storageAccountType”的“defaultValue”更新为“Standard_GRS”    。 请参阅下面的屏幕截图：

    ![Azure 资源管理器 Azure DevOps Azure Pipelines 更新 yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. 保存更改。
1. 从 Git Bash/Shell 运行以下命令，将更改推送到远程存储库。

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    第一个命令将本地存储库与远程存储库同步。 请记住，管道 YAML 文件已添加到远程存储库。

    更新远程存储库的主分支后，将再次发送管道。

若要验证所做的更改，可以检查存储帐户的 SKU。  请参阅[验证部署](#verify-the-deployment)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

你可能还需要删除 GitHub 存储库和 Azure DevOps 项目。

## <a name="next-steps"></a>后续步骤

在本教程中，你将创建 Azure DevOps 管道以部署 Azure 资源管理器模板。 若要了解如何跨多个区域部署 Azure 资源，以及如何使用安全部署做法，请参阅

> [!div class="nextstepaction"]
> [使用安全部署做法](./deployment-manager-tutorial.md)
