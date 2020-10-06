---
title: 使用 GitHub 操作将静态站点部署到 Azure 存储
description: 托管 GitHub 操作的 Azure 存储静态网站
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 9b616f8df0a8b64969fac1d18544e6d316120e50
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771031"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>在 Azure 存储中设置 GitHub 操作工作流以部署静态网站

通过使用工作流将静态站点部署到 Azure 存储 blob 开始使用 [GitHub 操作](https://docs.github.com/en/actions) 。 设置 GitHub 操作工作流后，可以在更改网站的代码时，从 GitHub 自动将网站部署到 Azure。 

> [!NOTE]
> 如果你使用的是 [Azure 静态 Web 应用](https://docs.microsoft.com/azure/static-web-apps/)，则无需手动设置 GitHub 操作工作流。
> Azure 静态 Web 应用会自动为你创建 GitHub 工作流。 

## <a name="prerequisites"></a>先决条件

Azure 订阅和 GitHub 帐户。 

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 具有静态网站代码的 GitHub 帐户。 如果没有 GitHub 帐户，请 [免费注册](https://github.com/join)。  
- 托管在 Azure 存储中的工作静态网站。 了解如何 [在 Azure 存储中承载静态网站](storage-blob-static-website-how-to.md)。 静态网站应包含 [Azure CDN](static-website-content-delivery-network.md)。

## <a name="generate-deployment-credentials"></a>生成部署凭据

可以在[Azure CLI](/cli/azure/)中使用[az ad sp 创建-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true)命令创建[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 使用 Azure 门户中 [Azure Cloud Shell](https://shell.azure.com/) 或选择 " **试用** " 按钮来运行此命令。

将占位符替换 `myStaticSite` 为 Azure 存储中托管的网站的名称。 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
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
> 始终应授予最小访问权限。 上一示例中的范围限制为特定的应用服务应用，而不是整个资源组。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

1. 在 [GitHub](https://github.com/)中，浏览存储库。

1. 选择 " **设置" > 机密 > 新机密**。

1. 将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

    以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如： 。

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>添加工作流

1. 请参阅 GitHub 存储库的 " **操作** "。 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub 操作菜单项&quot;:::

1. 选择 &quot; **自行设置工作流**&quot;。 

1. 删除工作流文件的部分后面的所有内容 `on:` 。 例如，剩余工作流可能如下所示。 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重命名工作流 `Blob storage website CI` 并添加结帐和登录操作。 这些操作将签出你的站点代码，并使用之前创建的 GitHub 机密对 Azure 进行身份验证 `AZURE_CREDENTIALS` 。 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. 使用 Azure CLI 操作将代码上传到 blob 存储，并清除 CDN 终结点。 对于 `az storage blob upload-batch` ，请将占位符替换为你的存储帐户名称。 脚本将上传到该 `$web` 容器。 对于 `az cdn endpoint purge` ，请将占位符替换为 cdn 配置文件名称、cdn 终结点名称和资源组。

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. 通过添加操作来注销 Azure 来完成工作流。 下面是已完成的工作流。 文件将显示在 `.github/workflows` 存储库的文件夹中。

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>查看部署

1. 请参阅 GitHub 存储库的 " **操作** "。 

1. 打开第一个结果，以查看工作流运行的详细日志。 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub 操作菜单项&quot;:::

1. 选择 &quot; **自行设置工作流**&quot;。 

1. 删除工作流文件的部分后面的所有内容 `on:` 。 例如，剩余工作流可能如下所示。 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重命名工作流 `Blob storage website CI` 并添加结帐和登录操作。 这些操作将签出你的站点代码，并使用之前创建的 GitHub 机密对 Azure 进行身份验证 `AZURE_CREDENTIALS` 。 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. 使用 Azure CLI 操作将代码上传到 blob 存储，并清除 CDN 终结点。 对于 `az storage blob upload-batch` ，请将占位符替换为你的存储帐户名称。 脚本将上传到该 `$web` 容器。 对于 `az cdn endpoint purge` ，请将占位符替换为 cdn 配置文件名称、cdn 终结点名称和资源组。

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. 通过添加操作来注销 Azure 来完成工作流。 下面是已完成的工作流。 文件将显示在 `.github/workflows` 存储库的文件夹中。

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>清理资源

当不再需要 Azure 静态站点和存储库时，请通过删除资源组和 GitHub 存储库来清理部署的资源。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 静态 Web 应用](https://docs.microsoft.com/azure/static-web-apps/)