---
title: 使用 CLI 创建、查看和管理 Azure 事件网格系统主题
description: 本文介绍如何使用 Azure CLI 创建、查看和删除系统主题。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 354afb89b145e288f525e40ad700e8f8a67c6dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115037"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>使用 Azure CLI 创建、查看和管理事件网格系统主题
本文说明如何使用 Azure CLI 创建和管理系统主题。 有关系统主题的概述，请参阅[系统主题](system-topics.md)。

## <a name="install-extension-for-azure-cli"></a>安装适用于 Azure CLI 的扩展
对于 Azure CLI，需要[事件网格扩展](/cli/azure/azure-cli-extensions-list)。

在 Cloud Shell 中：

- 如果以前安装了该扩展，请对其进行更新： `az extension update -n eventgrid`
- 如果你之前未安装该扩展，请安装它：  `az extension add -n eventgrid`

对于本地安装：

1. [安装 Azure CLI](/cli/azure/install-azure-cli)。 请通过使用 `az --version` 进行检查来确保安装了最新版本。
2. 卸载旧版扩展：`az extension remove -n eventgrid`
3. 使用 `az extension add -n eventgrid` 安装 eventgrid 扩展

## <a name="create-a-system-topic"></a>创建系统主题

- 若要先在 Azure 源上创建系统主题，然后再为该主题创建事件订阅，请参阅以下参考主题：
    - [az eventgrid system-topic create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        有关可用于创建系统主题的 `topic-type` 值的列表，请运行以下命令。 这些主题类型值表示支持创建系统主题的事件源。 请忽略列表中的 `Microsoft.EventGrid.Topics` 和 `Microsoft.EventGrid.Domains`。 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- 若要在创建 Azure 源的事件订阅时创建系统主题（以隐式方式），请使用 [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) 方法。 下面是一个示例：
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    有关分步说明的教程，请参阅[订阅存储帐户](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)。

## <a name="view-all-system-topics"></a>查看所有系统主题
若要查看所有系统主题以及所选系统主题的详细信息，请使用以下命令：

- [az eventgrid system-topic list](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>删除系统主题
若要删除系统主题，请使用以下命令： 

- [az eventgrid system-topic delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>后续步骤
请参阅 [Azure 事件网格中的系统主题](system-topics.md)部分，详细了解 Azure 事件网格支持的系统主题和主题类型。 