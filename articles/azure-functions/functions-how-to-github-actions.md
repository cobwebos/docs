---
title: 使用 GitHub 操作在 Azure Functions 中进行代码更新
description: 了解如何在 GitHub 中使用 GitHub 操作来定义用于生成和部署 Azure Functions 项目的工作流。
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 8e9e1189c3eb9de273926645ad0d4cfde5ba1c49
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260046"
---
# <a name="continuous-delivery-by-using-github-action"></a>使用 GitHub 操作进行持续交付

[GitHub 操作](https://github.com/features/actions)允许您定义一个工作流，以便自动生成函数代码并将其部署到 Azure 中的函数应用。 

> [!IMPORTANT]  
> GitHub 操作当前为 beta 版本。 必须首先[注册，才能](https://github.com/features/actions)使用 GitHub 帐户加入预览版。

在 GitHub 操作中，[工作流](https://help.github.com/articles/about-github-actions#workflow)是在 github 存储库中定义的自动化过程。 此过程告知 GitHub 如何在 GitHub 上生成和部署函数应用项目。 

工作流是由存储库`/.github/workflows/`路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含组成工作流的各种步骤和参数。 

对于 Azure Functions 工作流，文件包含三个部分： 

| 节 | 任务 |
| ------- | ----- |
| **身份验证** | <ol><li>定义服务主体。</li><li>创建 GitHub 机密。</li></ol>|  
| **生成** | <ol><li>设置环境。</li><li>生成函数应用。</li></ol> |
| **部署** | <ol><li>部署函数应用。</li></ol>| 

## <a name="create-a-service-principal"></a>创建服务主体

你可以使用[Azure CLI](/cli/azure/)中的[az ad sp 创建-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令来创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此示例中，将资源中的占位符替换为你的订阅 ID、资源组和函数应用名称。 输出是提供对函数应用程序的访问权限的角色分配凭据。 复制此 JSON 对象，该对象可用于从 GitHub 进行身份验证。

> [!IMPORTANT]
> 授予最小访问权限始终是一种很好的做法。 这就是上一个示例中的作用域仅限于特定的函数应用，而不是整个资源组。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

1. 在[GitHub](https://github.com)中浏览存储库，选择 "**设置** > " "**机密** > " "**添加新密钥**"。

    ![添加机密](media/functions-how-to-github-actions/add-secret.png)

1. 将`AZURE_CREDENTIALS`用作**值**的 "**名称**" 和 "复制的命令输出"，然后选择 "**添加密钥**"。 

GitHub 现在可以在 Azure 中的函数应用上进行身份验证。

## <a name="set-up-the-environment"></a>设置环境 

可以使用一个 "发布" 设置操作来设置环境。

|语言 | 设置操作 |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

以下示例显示了为各种受支持的语言设置环境的工作流部分：

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

这取决于语言和 Azure Functions 支持的语言，此部分应为每种语言的标准生成步骤。

下面的示例以各种支持的语言显示了生成函数应用的工作流部分。：

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

若要将代码部署到 function app，需要使用`Azure/functions-action`操作。 此操作有两个参数：

|参数 |说明  |
|---------|---------|
|**_应用名称_** | 必需函数应用的名称。 |
|_**槽名称**_ | 可有可无要部署到的[部署槽](functions-deployment-slots.md)的名称。 必须已在 function app 中定义槽。 |


下面的示例使用版本 1 `functions-action`：

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>后续步骤

若要查看完整的 yaml，请参阅[Azure GitHub 操作工作流](https://github.com/Azure/actions-workflow-samples)中具有`functionapp`名称的存储库中的一个文件。 您可以使用这些示例作为工作流的起点。

> [!div class="nextstepaction"]
> [了解有关 GitHub 操作的详细信息](https://help.github.com/en/articles/about-github-actions)
