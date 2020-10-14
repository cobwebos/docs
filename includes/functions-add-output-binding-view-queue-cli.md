---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606198"
---
可以在 [Azure 门户](../articles/storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)中查看队列。 也可以按以下步骤中所述，在 Azure CLI 中查看队列：

1. 打开函数项目的 *local.setting.json* 文件，并复制连接字符串值。 在终端或命令窗口中运行以下命令，以创建名为 `AZURE_STORAGE_CONNECTION_STRING` 的环境变量（请在 `<MY_CONNECTION_STRING>` 位置粘贴特定的连接字符串）。 （创建此环境变量后，无需在使用 `--connection-string` 参数的每个后续命令中提供连接字符串。）

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. （可选）使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令查看帐户中的存储队列。 此命令的输出应包含名为 `outqueue` 的队列，该队列是函数将其第一条消息写入该队列时创建的。
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. 使用 [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) 命令读取此队列中的消息，队列名称应是前面在测试函数时使用的名称。 该命令读取再删除该队列中的第一条消息。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    此脚本使用 certutil 解码本地临时文件中的 base64 编码消息集合。 如果没有任何输出，请尝试从脚本中删除 `> NUL`，以阻止在出错时隐藏 certutil 输出。 
    
    ---
    
    由于消息正文是以 [base64 编码](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)格式存储的，因此，必须解码消息才能显示消息。 执行 `az storage message get` 后，该消息将从队列中删除。 如果 `outqueue` 中只有一条消息，则再次运行此命令时不会检索到消息，而是收到错误。
