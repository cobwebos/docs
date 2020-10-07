---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482111"
---
1. 使用 `az group create` 命令以创建资源组：

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    如果愿意，你可以将“eastus”更改为离你更近的位置。

1. 使用 `az keyvault create` 创建密钥保管库：

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    将 `<your-unique-keyvault-name>` 替换为在整个 Azure 中均唯一的名称。 通常使用个人或公司名称以及其他数字和标识符。 

1. 创建用于向代码提供 Key Vault 名称的环境变量：

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
