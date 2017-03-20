---
title: "在 Azure 中不到&5; 分钟创建你的第一个 Python Web 应用 | Microsoft 文档"
description: "了解如何通过部署一个示例 Python 应用，轻松地在应用服务中运行 Web 应用。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: dbacf368684bf23f63d997941ac68cf0bc1d9a40
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>在 Azure 中不到&5; 分钟创建你的第一个 Python Web 应用
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

本快速入门帮助你在数分钟内将你的第一个 Python Web 应用部署到 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)。

在开始本快速入门之前，请确保 [Azure CLI 已安装](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)在计算机上。

## <a name="create-a-python-web-app-in-azure"></a>在 Azure 中创建 Python Web 应用
2. 运行 `az login` 并按屏幕说明进行操作，以便登录到 Azure。
   
    ```azurecli
    az login
    ```
   
3. 创建[资源组](../azure-resource-manager/resource-group-overview.md)。 这是放置所有 Azure 资源（例如 Web 应用及其 SQL 数据库后端）的地方，这些资源需要集中进行管理。

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    若要查看适用于 `---location` 的可能值，请使用 `az appservice list-locations` Azure CLI 命令。

3. 创建“免费”[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. 使用 `<app_name>` 中的唯一名称创建新的 Web 应用。

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. 从 GitHub 部署示例 Python 应用。

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
    ```

5. 若要查看应用在 Azure 中的实时运行情况，请运行此命令。

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

恭喜，你的第一个 Python Web 应用已在 Azure 应用服务中实时运行！

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

浏览预先创建的 [Web 应用 CLI 脚本](app-service-cli-samples.md)。

