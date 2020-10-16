---
title: 使用 GitHub Actions 部署资源管理器模板
description: 介绍如何使用 GitHub Actions 部署资源管理器模板。
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure,subject-armqs
ms.openlocfilehash: b5852a65b4ed3c7cc73352fed37eeff035f8563c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106784"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>使用 GitHub Actions 部署 Azure 资源管理器模板

[Github 操作](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)是 GitHub 中的一个功能套件，可以在存储代码的同一位置自动执行软件开发工作流，并针对拉取请求和问题进行协作。

使用 " [部署 azure 资源管理器模板" 操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) 将资源管理器模板自动部署到 Azure。 

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有，请 [免费](https://github.com/join)注册。  
    - 用于存储资源管理器模板和工作流文件的 GitHub 存储库。 若要创建一个存储库，请参阅[创建新存储库](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)。


## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件包含两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体。 <br /> 2. 创建 GitHub 机密。 |
|**部署** | 1. 部署资源管理器模板。 |

## <a name="generate-deployment-credentials"></a>生成部署凭据


可以在[Azure CLI](/cli/azure/)中使用[az ad sp 创建-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true)命令创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 使用 Azure 门户中 [Azure Cloud Shell](https://shell.azure.com/) 或选择 " **试用** " 按钮来运行此命令。

将占位符替换 `myApp` 为应用程序的名称。 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

在上面的示例中，将占位符替换为你的订阅 ID 和资源组名称。 输出是一个具有角色分配凭据的 JSON 对象，该对象提供对应用服务应用的访问权限，如下所示。 稍后复制此 JSON 对象。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 始终应授予最小访问权限。 上一示例中的范围限制为资源组。



## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

需要为 Azure 凭据、资源组和订阅创建机密。 

1. 在 [GitHub](https://github.com/)中，浏览存储库。

1. 选择 " **设置" > 机密 > 新机密**。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称 `AZURE_CREDENTIALS` 。

1. 创建另一个名为 `AZURE_RG` 的机密。 将资源组的名称添加到机密的值字段。 

1. 创建一个名为的其他机密 `AZURE_SUBSCRIPTION` 。 将订阅 ID 添加到机密的值字段。 

## <a name="add-resource-manager-template"></a>添加资源管理器模板

将资源管理器模板添加到 GitHub 存储库。 此模板创建存储帐户。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

你可以将该文件放到存储库中的任何位置。 下一节中的 "工作流" 示例假定模板文件命名为 " **azuredeploy.js**"，并存储在存储库的根目录中。

## <a name="create-workflow"></a>创建工作流

工作流文件必须存储在存储库根目录的“.github/workflow”文件夹中。 工作流文件扩展名可以是“.yml”或“.yaml”。 

1. 在 GitHub 存储库的顶部菜单中，选择“操作”。
1. 选择“新建工作流”。
1. 选择“自己设置工作流”。
1. 如果希望使用“main.yml”以外的其他名称，请重命名工作流文件。 例如：“deployStorageAccount.yml”。
1. 将 yml 文件的内容替换为以下内容：

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@master

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - uses: azure/arm-deploy@v1
        - name: Run ARM deploy
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```

    工作流文件的第一部分包括：

    - **name**：工作流的名称。
    - 事件：触发工作流的 GitHub 事件的名称。 当主分支上有推送事件时，将触发工作流，修改所指定的两个文件中的至少一个。 这两个文件分别是工作流文件和模板文件。

1. 选择“开始提交”。
1. 选择“直接提交到主分支”。
1. 选择“提交新文件”（或“提交更改”）。 

由于工作流配置为由要更新的工作流文件或模板文件触发，因此在提交更改后，工作流将立即启动。

## <a name="check-workflow-status"></a>检查工作流状态

1. 选择 " **操作** " 选项卡。你将看到列出的 " **创建 deployStorageAccount. docker-compose.override.yml** " 工作流。 运行工作流需要1-2 分钟。
1. 选择要打开的工作流。
1. 从菜单中选择 " **运行 ARM 部署** " 以验证部署。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和存储库，请通过删除资源组和 GitHub 存储库来清理部署的资源。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建第一个 ARM 模板](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
