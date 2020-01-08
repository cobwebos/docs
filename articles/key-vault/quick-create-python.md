---
title: 快速入门 - 适用于 Python 的 Azure Key Vault 客户端库
description: 了解如何使用 Python 客户端库在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: a514dbce91a98bd51e51b1724d631bc224b2f33a
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75527920"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>快速入门：适用于 Python 的 Azure Key Vault 客户端库

适用于 Python 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 使用适用于 Python 的 Key Vault 客户端库可以：

- 提高安全性以及控制密钥和密码。
- 在几分钟内创建并导入加密密钥。
- 通过云扩展和全局冗余减少延迟。
- 简化和自动化与 SSL/TLS 证书相关的任务。
- 使用 FIPS 140-2 第 2 级验证的 HSM。

[API 参考文档](/python/api/overview/azure/key-vault?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [包（Python 包索引）](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>必备条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 或更高版本
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/overview)

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

本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](quick-create-cli.md)、[Azure PowerShell 快速入门](quick-create-powershell.md)或 [Azure 门户快速入门](quick-create-portal.md)中的步骤创建 Key Vault。 或者，可运行以下 Azure CLI 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>创建服务主体

对基于云的 .NET 应用程序进行身份验证的最简单方法是使用托管标识；有关详细信息，请参阅[使用应用服务托管标识访问 Azure Key Vault](managed-identity.md)。 不过，为简单起见，本快速入门将创建一个 .NET 控制台应用程序。 在 Azure 中对桌面应用程序进行身份验证需要使用服务主体和访问控制策略。

使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建服务主体：

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

此操作将返回一系列键/值对。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

请记下 clientId 和 clientSecret，因为在下面的[设置环境变量](#set-environmental-variables)步骤中将要用到。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

通过将 clientId 传递给 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，为密钥保管库创建授予服务主体权限的访问策略。 授予服务主体对密钥和机密的 get、list 和 set 权限。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>设置环境变量

应用程序中的 DefaultAzureCredential 方法依赖于三个环境变量：`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID`。 请使用 `export VARNAME=VALUE` 格式将这些变量设置为在上述[创建服务主体](#create-a-service-principal)步骤中记下的 clientId、clientSecret 和 tenantId 值。 （该方法只为当前 shell 和从 shell 创建的进程设置变量；若要将这些变量永久添加到环境中，请编辑 `/etc/environment ` 文件。） 

还需要将密钥保管库名称另存为名为 `KEY_VAULT_NAME` 的环境变量。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

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
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print("Your secret is '" + secretValue + "'.")

print("Retrieving your secret from " + keyVaultName + ".")

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.")
print("Deleting your secret from " + keyVaultName + " ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault、存储了一个机密，然后检索了该机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](key-vault-overview.md)
- 参阅 [Azure Key Vault 开发人员指南](key-vault-developers-guide.md)
- 了解[密钥、机密和证书](about-keys-secrets-and-certificates.md)
- 查看 [Azure Key Vault 最佳做法](key-vault-best-practices.md)
