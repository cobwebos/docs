---
title: 快速入门：使用 Azure CLI 创建 Synapse 工作区
description: 按照本指南中的步骤使用 Azure CLI 创建 Synapse 工作区。
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7f0bf7d409c5e47abaaa5b59271f55952d8ccff4
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871576"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure Synapse 工作区

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 可以在浏览器中将它与 Azure Cloud Shell 配合使用。 也可将它安装在 macOS、Linux 或 Windows 上，然后从命令行运行它。

本快速入门介绍如何使用 Azure CLI 创建 Synapse 工作区。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- 下载并安装 [jq](https://stedolan.github.io/jq/download/)，它是一种灵活的轻量级命令行 JSON 处理器
- [Azure Data Lake Storage Gen2 存储帐户](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Azure Synapse 工作区需要能够读取所选 ADLS Gen2 帐户以及向其写入内容。 此外，对于作为主存储帐户链接的任何存储帐户，在创建存储帐户时必须已启用“分层命名空间”，如[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)页面上所述。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>在本地安装 Azure CLI

如果选择在本地安装和使用 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

如果在本地运行 Azure CLI，则必须登录并进行身份验证。 如果使用 Azure Cloud Shell，则不需执行此步骤。 若要登录到 Azure CLI，请在浏览器窗口中运行 `az login` 并进行身份验证：

```azurecli
az login
```

有关使用 Azure CLI 进行身份验证的详细信息，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="install-azure-synapse-extension-for-azure-cli"></a>安装适用于 Azure CLI 的 Azure Synapse 扩展

```azurecli
az extension add --name synapse
```

> [!WARNING]
> 适用于 Azure CLI 的 Azure Synapse 扩展现为预览版状态。

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure Synapse 工作区

1. 定义创建 Azure Synapse 工作区资源所必需的环境变量。

    | 环境变量名称 | 说明 |
    |---|---|---|
    |StorageAccountName| 现有 ADLS Gen2 存储帐户的名称。|
    |StorageAccountResourceGroup| 现有 ADLS Gen2 存储帐户资源组的名称。 |
    |FileShareName| 现有存储文件系统的名称。|
    |SynapseResourceGroup| 为 Azure Synapse 资源组选择新名称。 |
    |区域| 选择其中一个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/#overview)。 |
    |SynapseWorkspaceName| 为新的 Azure Synapse 工作区选择唯一名称。 |
    |SqlUser| 为新用户名选择一个值。|
    |SqlPassword| 选择安全密码。|
    |||

2. 创建资源组作为 Azure Synapse 工作区的容器：
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. 检索 ADLS Gen 2 存储帐户密钥：
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. 检索 ADLS Gen 2 存储终结点 URL：
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. （可选）始终可查看 ADLS Gen2 存储帐户密钥和终结点：
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. 创建 Azure Synapse 工作区：
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. 获取 Azure Synapse 工作区的 Web 和开发 URL：
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. 创建防火墙规则以允许你从自己的计算机访问 Azure Synapse 工作区：

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. 打开环境变量 `WorkspaceWeb` 中存储的 Azure Synapse 工作区 Web URL 地址以访问工作区：

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Azure Synapse 工作区 Web](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>清理资源

执行以下步骤，删除 Azure Synapse 工作区。
> [!WARNING]
> 如果删除 Azure Synapse 工作区，将一并删除分析引擎，还将删除存储在包含的 SQL 池和工作区元数据所在的数据库中的数据。 删除后，该工作区将无法再连接到 SQL 或 Apache Spark 终结点。 将删除所有代码项目（查询、笔记本、作业定义和管道）。
>
> 删除工作区不会影响链接到工作区的 Data Lake Store Gen2 中的数据。

如果要删除 Azure Synapse 工作区，请完成以下命令：

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>后续步骤

接下来，可以[创建 SQL 池](quickstart-create-sql-pool-studio.md)或[创建 Apache Spark 池](quickstart-create-apache-spark-pool-studio.md)，开始分析和探究你的数据。
