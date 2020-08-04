---
title: 快速入门 – Azure Key Vault Python 客户端库 – 管理机密
description: 了解如何使用 Python 客户端库在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: 135ad450f7b0491200aeafd470e7a551d577e96a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285539"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>快速入门：适用于 Python 的 Azure Key Vault 机密客户端库

适用于 Python 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 使用适用于 Python 的 Key Vault 客户端库可以：

- 提高安全性以及控制密钥和密码。
- 在几分钟内创建并导入加密密钥。
- 通过云扩展和全局冗余减少延迟。
- 简化和自动化与 TLS/SSL 证书相关的任务。
- 使用 FIPS 140-2 第 2 级验证的 HSM。

[API 参考文档](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [包（Python 包索引）](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 或更高版本
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入门假设你在 Linux 终端窗口中运行 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="setting-up"></a>设置

### <a name="install-the-package"></a>安装包

在控制台窗口中，安装适用于 Python 的 Azure Key Vault 机密库。

```console
pip install azure-keyvault-secrets
```

本快速入门还需要安装 azure.identity 包：

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>创建服务主体

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>设置环境变量

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>对象模型

使用适用于 Python 的 Azure Key Vault 客户端库，你可以管理密钥和相关的资产（例如证书和机密）。 以下代码示例演示如何创建客户端以及设置、检索和删除机密。

[https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app ) 中提供了整个控制台应用。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

向密钥保管库进行身份验证和创建密钥保管库客户端，依赖于上面[设置环境变量](#set-environmental-variables)步骤中的环境变量。 密钥保管库的名称将扩展为密钥保管库 URI，格式为“https://<你的密钥保管库名称>.vault.azure.net”。

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>保存机密

应用程序通过身份验证后，你可以使用 client.SetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)将机密放入 keyvault。此操作需要使用机密的名称，本示例中使用“mySecret”。  

```python
client.set_secret(secretName, secretValue)
```

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>检索机密

现在，可以使用 [client.GetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)检索以前设置的值。

```python
retrieved_secret = client.get_secret(secretName)
 ```

机密现已保存为 `retrieved_secret.value`。

### <a name="delete-a-secret"></a>删除机密

最后，使用 [client.DeleteSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)从密钥保管库中删除机密。

```python
client.delete_secret(secretName)
```

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令来验证是否已删除机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>示例代码

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault、存储了一个机密，然后检索了该机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
