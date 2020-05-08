---
title: 使用 GitHub 操作部署资源管理器模板
description: 介绍如何使用 GitHub 操作部署 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875189"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>使用 GitHub 操作部署 Azure 资源管理器模板

[Github 操作](https://help.github.com/en/actions)使你可以直接在 github 存储库中创建 Azure 资源管理器（ARM）模板的自定义软件开发生命周期工作流。 [工作流](https://help.github.com/actions/reference/workflow-syntax-for-github-actions)由 YAML 文件定义。 工作流具有一个或多个作业，每个作业都包含一组执行单个任务的步骤。 步骤可以运行命令或使用操作。 你可以创建自己的操作或使用[GitHub 社区](https://github.com/marketplace?type=actions)共享的操作，并根据需要对其进行自定义。 本文介绍如何使用[Azure CLI 操作](https://github.com/marketplace/actions/azure-cli-action)部署资源管理器模板。

Azure CLI 操作具有两个相关操作：

- **[签](https://github.com/marketplace/actions/checkout)** 出：签出存储库，使工作流可以访问任何指定的资源管理器模板。
- **[Azure 登录](https://github.com/marketplace/actions/azure-login)**：用 Azure 凭据登录

部署资源管理器模板的基本工作流可能有三个步骤：

1. 签出模板文件。
2. 登录 Azure。
3. 部署资源管理器模板

## <a name="prerequisites"></a>先决条件

需要 GitHub 存储库来存储资源管理器模板和工作流文件。 若要创建一个存储库，请参阅[创建新的存储库](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)。

## <a name="configure-deployment-credentials"></a>配置部署凭据

Azure 登录操作使用服务主体对 Azure 进行身份验证。 CI/CD 工作流的主体通常需要内置参与者才能部署 Azure 资源。

以下 Azure CLI 脚本显示了如何使用对 Azure 资源组的参与者权限生成 Azure 服务主体。 此资源组是工作流部署资源管理器模板中定义的资源的位置。

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

自定义脚本中 **$projectName**和 **$location**的值。 资源组名称是追加了 **rg** 的项目名称。 需要在工作流中指定资源组名称。

此脚本输出类似于下面的 JSON 对象：

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

复制 JSON 输出，并将其作为 GitHub 机密存储在 GitHub 存储库中。 如果还没有存储库，请参阅[必备组件](#prerequisites)。

1. 从 GitHub 存储库中，选择 "**设置**" 选项卡。
1. 从左侧菜单中选择 "**机密**"。
1. 输入以下值：

    - **名称**： AZURE_CREDENTIALS
    - **值**：（粘贴 JSON 输出）
1. 选择 "**添加密钥**"。

你需要指定工作流中的机密名称。

## <a name="add-resource-manager-template"></a>添加资源管理器模板

将资源管理器模板添加到 GitHub 存储库。 如果没有，可以使用以下模板。 该模板将创建一个存储帐户。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

你可以将该文件放到存储库中的任何位置。 下一节中的工作流示例假定模板文件名为**azuredeploy.json**，并将其存储在存储库根目录中名为**templates**的文件夹中。

## <a name="create-workflow"></a>创建工作流

工作流文件必须存储在存储库根目录的**github/workflow**文件夹中。 工作流文件扩展名可以是**docker-compose.override.yml**或**yaml**。

你可以创建工作流文件，然后将文件推送/上传到存储库，也可以使用以下过程：

1. 从 GitHub 存储库中，选择顶部菜单中的 "**操作**"。
1. 选择 "**新建工作流**"。
1. 选择 "**自行设置工作流**"。
1. 如果更喜欢**docker-compose.override.yml**以外的其他名称，请重命名工作流文件。 例如： **deployStorageAccount. docker-compose.override.yml**。
1. 将 docker-compose.override.yml 文件的内容替换为以下内容：

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    工作流文件包含三个部分：

    - **name**：工作流的名称。
    - **开启**：触发工作流的 GitHub 事件的名称。 当 master 分支上有推送事件时，工作流将同时修改所指定的两个文件中的至少一个。 这两个文件是工作流文件和模板文件。

        > [!IMPORTANT]
        > 验证两个文件及其路径是否匹配。
    - **作业**：工作流运行由一个或多个作业组成。 只有一个名为 "**部署存储帐户-模板**" 的作业。  此作业有三个步骤：

        - **签出源代码**。
        - **登录到 Azure**。

            > [!IMPORTANT]
            > 验证机密名称与你保存到存储库的内容是否匹配。 请参阅[配置部署凭据](#configure-deployment-credentials)。
        - **部署 ARM 模板**。 替换**resourceGroupName**的值。  如果使用了 "[配置部署凭据](#configure-deployment-credentials)" 中的 Azure CLI 脚本，则生成的资源组名称是附加了**rg**的项目名称。 验证**templateLocation**的值。

1. 选择 "**开始提交**"。
1. 选择 **"直接提交到主分支"**。
1. 选择 "**提交新文件**" 或 "**提交更改**"。

由于工作流配置为由要更新的工作流文件或模板文件触发，因此在提交更改后，工作流将立即启动。

## <a name="check-workflow-status"></a>检查工作流状态

1. 选择 "**操作**" 选项卡。应会看到 "**创建 deployStorageAccount. docker-compose.override.yml** " 工作流已列出。 执行工作流需要1-2 分钟。
1. 选择工作流将其打开。
1. 从左侧菜单中选择 "**部署-存储帐户-模板**（作业名称）"。 作业名称是在工作流中定义的。
1. 选择 "**部署 ARM 模板**（步骤名称）" 将其展开。 你可以看到 REST API 响应。

## <a name="next-steps"></a>后续步骤

如果需要通过分步教程来了解创建模板的过程，请参阅[教程：创建和部署第一个 ARM 模板](template-tutorial-create-first-template.md)。
