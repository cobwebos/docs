---
title: 快速入门：使用 CLI 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: ca4ffce1ae966d2bb8fb187c9b04938330969641
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286323"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>使用 CLI 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

本快速入门介绍如何使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 Azure CLI，本快速入门要求 Azure CLI 2.0.4 或更高版本。 请运行 `az --version` 检查版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="configure-the-cli-parameters"></a>配置 CLI 参数

如果在 Azure Cloud Shell 中运行命令，则不需要执行以下步骤。 如果在本地运行 CLI，请按以下步骤登录到 Azure 并设置当前订阅：

1. 运行以下命令来登录到 Azure：

    ```azurecli-interactive
    az login
    ```

2. 设置将在其中创建群集的订阅。 将 `MyAzureSub` 替换为要使用的 Azure 订阅的名称：

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

1. 请使用以下命令创建群集：

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | 名称 | *azureclitest* | 所需的群集名称。|
   | sku | *D13_v2* | 将用于群集的 SKU。 |
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |

    可以使用其他可选参数，例如群集的容量。

2. 运行以下命令，检查群集是否已成功创建：

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

如果结果包含带 `Succeeded` 值的 `provisioningState`，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 请使用以下命令创建数据库：

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster-name | *azureclitest* | 将在其中创建数据库的群集的名称。|
   | 名称 | *clidatabase* | 数据库名称。|
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |
   | soft-delete-period | *3650:00:00:00* | 供查询使用的数据的保留时间。 |
   | hot-cache-period | *3650:00:00:00* | 数据将在缓存中保留的时间。 |

2. 若要查看已创建的数据库，请运行以下命令：

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果计划学习其他快速入门和教程，请保留创建的资源。
* 若要清理资源，请删除群集。 删除群集时，也会删除其中的所有数据库。 使用以下命令删除群集：

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)
