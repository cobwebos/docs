---
title: 使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494788"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 在本文中，您创建的群集、 数据库使用 Azure CLI。

## <a name="prerequisites"></a>必备组件

若要完成本文，需要 Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您选择本地安装并使用 Azure CLI，本文需要 Azure CLI 2.0.4 或更高版本。 请运行 `az --version` 检查版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="configure-the-cli-parameters"></a>配置 CLI 参数

如果在 Azure Cloud Shell 中运行命令，则不需要执行以下步骤。 如果在本地运行 CLI，请按以下步骤登录到 Azure 并设置当前订阅：

1. 运行以下命令来登录到 Azure：

    ```azurecli-interactive
    az login
    ```

1. 设置将在其中创建群集的订阅。 将 `MyAzureSub` 替换为要使用的 Azure 订阅的名称：

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
   | name | *azureclitest* | 所需的群集名称。|
   | sku | *D13_v2* | 将用于群集的 SKU。 |
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |

    可以使用其他可选参数，例如群集的容量。

1. 运行以下命令，检查群集是否已成功创建：

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

如果结果包含带 `Succeeded` 值的 `provisioningState`，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 请使用以下命令创建数据库：

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster-name | *azureclitest* | 将在其中创建数据库的群集的名称。|
   | name | *clidatabase* | 数据库名称。|
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |
   | soft-delete-period | P365D | 表示供查询使用的数据的保留时间。 有关详细信息，请参阅[保留策略](/azure/kusto/concepts/retentionpolicy)。 |
   | hot-cache-period | P31D | 表示数据将在缓存中保留的时间。 有关详细信息，请参阅[缓存策略](/azure/kusto/concepts/cachepolicy)。 |

1. 若要查看已创建的数据库，请运行以下命令：

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果你打算按照我们的其他文章，请创建的资源。
* 若要清理资源，请删除群集。 删除群集时，也会删除其中的所有数据库。 使用以下命令删除群集：

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)
