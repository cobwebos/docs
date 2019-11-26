---
title: 使用 GitHub Actions 在 Azure Functions 中进行代码更新
description: 了解如何使用 GitHub Actions 来定义一个在 GitHub 中生成和部署 Azure Functions 项目的工作流。
author: ahmedelnably
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 18ba99077592a7d03e19fda86bc61e5839b82b5e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226915"
---
# <a name="continuous-delivery-by-using-github-action"></a>使用 Github Actions 进行持续交付

可以通过 [GitHub Actions](https://github.com/features/actions) 定义一个工作流，以便自动生成函数代码并将其部署到 Azure 中的函数应用。 

在 GitHub Actions 中，[工作流](https://help.github.com/articles/about-github-actions#workflow)是在 GitHub 存储库中定义的自动化过程。 此过程告知 GitHub 如何在 GitHub 中生成和部署函数应用项目。 

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。 

对于 Azure Functions 工作流，此文件有三个部分： 

| 部分 | 任务 |
| ------- | ----- |
| **身份验证** | <ol><li>定义服务主体。</li><li>下载发布配置文件。</li><li>创建 GitHub 机密。</li></ol>|
| **生成** | <ol><li>设置环境。</li><li>生成函数应用。</li></ol> |
| **部署** | <ol><li>部署函数应用。</li></ol>|

> [!NOTE]
> 如果决定使用发布配置文件进行身份验证，则不需创建服务主体。

## <a name="create-a-service-principal"></a>创建服务主体

可以在 [Azure CLI](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 中使用 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](/cli/azure/)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此示例中，请将资源中的占位符替换为你的订阅 ID、资源组以及函数应用名称。 输出是用于访问函数应用的角色分配凭据。 请复制此 JSON 对象，它可以用来从 GitHub 进行身份验证。

> [!IMPORTANT]
> 始终应授予最小访问权限。 这是在上一示例中将范围限制为特定的函数应用而不是整个资源组的原因。

## <a name="download-the-publishing-profile"></a>下载发布配置文件

可以下载函数应用的发布配置文件，只需转到应用的“概览”页并单击“获取发布配置文件”即可。

   ![下载发布配置文件](media/functions-how-to-github-actions/get-publish-profile.png)

复制文件内容。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

1. 在 [GitHub](https://github.com) 中浏览存储库，选择“设置” > “机密” > “添加新机密”。

   ![添加机密](media/functions-how-to-github-actions/add-secret.png)

1. 使用 `AZURE_CREDENTIALS` 作为**名称**，复制的命令输出作为**值**，然后选择“添加机密”。 如果使用发布配置文件，请使用 `SCM_CREDENTIALS` 作为**名称**，文件内容作为**值**。

GitHub 现在可以针对 Azure 中的函数应用进行身份验证了。

## <a name="set-up-the-environment"></a>设置环境 

可以使用发布设置操作之一来设置环境。

|语言 | 设置操作 |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

以下示例显示用于为各种受支持的语言设置环境的部分工作流：

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>生成函数应用

这取决于语言。对于 Azure Functions 支持的语言，应该可以将此部分视为每种语言的标准生成步骤。

以下示例显示的部分工作流用于在各种受支持的语言中生成函数应用：

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```

## <a name="deploy-the-function-app"></a>部署函数应用

若要将代码部署到函数应用，需使用 `Azure/functions-action` 操作。 该操作有两个参数：

|参数 |说明  |
|---------|---------|
|**_app-name_** | （必需）函数应用的名称。 |
|_**slot-name**_ | （可选）要部署到其中的[部署槽](functions-deployment-slots.md)的名称。 该槽必须已经在函数应用中定义。 |


以下示例使用第 1 版 `functions-action`：

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>后续步骤

若要查看完整的工作流 .yaml，请参阅 [Azure GitHub Actions 工作流示例存储库](https://aka.ms/functions-actions-samples)中名称包含 `functionapp` 的文件之一。 可以使用这些示例作为工作流的起点。

> [!div class="nextstepaction"]
> [详细了解 GitHub Actions](https://help.github.com/en/articles/about-github-actions)
