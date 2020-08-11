---
title: 教程 - 将 Azure Key Vault 与通过 Python 编写的虚拟机配合使用 | Microsoft Docs
description: 本教程介绍如何将 ASP.NET Core 应用程序配置为从 Key Vault 读取机密。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 29317e9d5972faf9326a17ebbbe83063f79cdf23
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512785"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>教程：将 Azure Key Vault 与通过 Python 编写的虚拟机配合使用

Azure Key Vault 可帮助你保护密钥、机密和证书，例如 API 密钥和数据库连接字符串。

在本教程中，会设置 Python 应用程序以使用 Azure 资源的托管标识从 Azure Key Vault 读取信息。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 创建密钥保管库
> * 在 Key Vault 中存储机密
> * 创建一个 Azure Linux 虚拟机
> * 为虚拟机启用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
> * 授予所需的权限，让控制台应用程序从 Key Vault 读取数据
> * 从 Key Vault 检索机密

在开始之前，请阅读 [Key Vault 的基本概念](basic-concepts.md)。 

如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

对于 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * 本教程要求在本地运行 Azure CLI。 必须安装 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>使用机密填充密钥保管库

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用以下方法之一创建名为 myVM 的 VM：

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure 门户](../../virtual-machines/linux/quick-create-portal.md) | [Azure 门户](../../virtual-machines/windows/quick-create-portal.md) |

若要使用 Azure CLI 创建 Linux VM，请使用 [az vm create](/cli/azure/vm) 命令。  以下示例添加一个名为 azureuser 的用户帐户。 `--generate-ssh-keys` 参数用来自动生成一个 SSH 密钥，并将其放置在默认密钥位置 ( *~/.ssh*) 中。 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

请记录输出中 `publicIpAddress` 的值。

## <a name="assign-an-identity-to-the-vm"></a>为 VM 分配标识

使用 Azure CLI [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令为虚拟机创建系统分配的标识：

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

记下以下代码中显示的系统分配的标识。 以上命令的输出为： 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>为 VM 标识分配权限

现在可以运行以下命令，将前面创建的标识权限分配到 Key Vault：

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>登录 VM

若要登录到虚拟机，请按照[连接并登录到运行 Linux 的 Azure 虚拟机](../../virtual-machines/linux/login-using-aad.md)或[连接并登录到运行 Windows 的 Azure 虚拟机](../../virtual-machines/windows/connect-logon.md)中的说明操作。


若要登录到 Linux VM，可以使用 ssh 命令以及在[创建虚拟机](#create-a-virtual-machine)步骤中提供的“<publicIpAddress>”：

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>在 VM 上安装 Python 库

在虚拟机上，安装将在 Python 脚本中使用的两个 Python 库：`azure-keyvault-secrets` 和 `azure.identity`。  

例如在 Linux VM 上，可以使用 `pip3` 安装这些库：

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>创建并编辑示例 Python 脚本

在虚拟机上，创建名为 sample.py 的 Python 文件。 编辑文件以包含以下代码，将“<your-unique-keyvault-name>”替换为 Key Vault 的名称：

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>运行示例 Python 应用

最后，运行 sample.py。 如果一切正常，应返回机密值：

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的虚拟机和 Key Vault 时，请将其删除。  只需删除它们所属的资源组，即可快速执行此操作：

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
