---
title: 快速入门：通过 Azure CLI 使用 Azure Static Web Apps 生成你的第一个静态 Web 应用
description: 了解如何使用 Azure Static Web Apps CLI 生成 Azure Static Web Apps 实例。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 7e0fdbc50dd36e4ea23903a5929735c1c83bd394
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752797"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>快速入门：使用 Azure CLI 生成你的第一个静态 Web 应用

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此快速入门中，你会使用 Azure CLI 将 Web 应用部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [GitHub 个人访问令牌](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) 帐户
- 已安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（版本 2.8.0 或更高版本）

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下来，使用以下命令切换到新文件夹。

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure CLI 创建静态 Web 应用。

> [!IMPORTANT]
> 确保你处于终端的“my-first-static-web-app”文件夹中。

1. 使用以下命令登录到 Azure CLI。

    ```bash
    az login
    ```

1. 从存储库创建新的静态 Web 应用。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`：将此值替换为现有的 Azure 资源组名称。

    - `<YOUR_GITHUB_ACCOUNT_NAME>`：将此值替换为你的 GitHub 用户名。

    - `<LOCATION>`：将此值替换为离你最近的位置。 选项包括：CentralUS、EastAsia、EastUS2、WestEurope 和 WestUS2    。

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`：将此值替换为先前生成的 [GitHub 个人访问令牌](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)。

    你现在可以在 Azure 中查看创建的应用。

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在顶部搜索栏中搜索“my-first-web-static-app”。

1. 选择“my-first-web-static-app”。

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可以运行以下命令以删除 Azure Static Web Apps 实例：

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
