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
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093335"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>教程：将 Azure Key Vault 与通过 Python 编写的虚拟机配合使用

Azure Key Vault 可以帮助保护机密，例如访问应用程序、服务和 IT 资源时所需的 API 密钥与数据库连接字符串。

本教程介绍如何获取控制台应用程序，以便从 Azure Key Vault 读取信息。 为此，请将托管标识用于 Azure 资源。 

本教程介绍如何：

> [!div class="checklist"]
> * 创建密钥保管库。
> * 将机密添加到 Key Vault。
> * 从密钥保管库检索机密。
> * 创建一个 Azure 虚拟机。
> * 启用托管标识。
> * 为 VM 标识分配权限。

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

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>向密钥保管库添加机密

我们将添加机密以帮助说明这是如何工作的。 机密可以是 SQL 连接字符串，或者需要安全保存的、可供应用程序使用的其他任何信息。

若要在名为 **AppSecret** 的 Key Vault 中创建机密，请输入以下命令：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

此机密将存储值 **MySecret**。

## <a name="create-a-virtual-machine"></a>创建虚拟机
可以使用以下方法之一创建虚拟机：

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure 门户](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>为 VM 分配标识
在此步骤中，请通过在 Azure CLI 中运行以下命令，为虚拟机创建系统分配的标识：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
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
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>登录到虚拟机

若要登录到虚拟机，请按[连接并登录到运行 Windows 的 Azure 虚拟机](../../virtual-machines/windows/connect-logon.md)中的说明操作。

## <a name="create-and-run-a-sample-python-app"></a>创建并运行示例 Python 应用

下一部分演示名为 *Sample.py* 的示例文件。 该文件使用 [requests](https://2.python-requests.org/en/master/) 库发出 HTTP GET 调用。

## <a name="edit-samplepy"></a>编辑 Sample.py

创建 *Sample.py* 后，打开该文件并复制本部分中的代码。 

该代码演示一个两步过程：
1. 从 VM 上的本地 MSI 终结点获取一个令牌。  
  这还会从 Azure AD 获取令牌。
1. 将令牌传递到 Key Vault，然后获取机密。 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

可以通过运行以下代码来显示机密值： 

```console
python Sample.py
```

以上代码演示了如何在 Windows 虚拟机中通过 Azure Key Vault 执行操作。 

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的虚拟机和 Key Vault 时，请将其删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
