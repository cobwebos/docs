---
title: 快速入门 - 适用于 .NET 的 Azure Key Vault 客户端库 (v4)
description: 了解如何使用 .NET 客户端库 (v4) 在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 84f89b9def3fc5d98ebb669b9f5b86523854f7a8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185717"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>快速入门：适用于 .NET 的 Azure Key Vault 客户端库 (SDK v4)

适用于 .NET 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 使用适用于 .NET 的 Key Vault 客户端库可以：

- 提高安全性以及控制密钥和密码。
- 在几分钟内创建并导入加密密钥。
- 通过云扩展和全局冗余减少延迟。
- 简化和自动化与 TLS/SSL 证书相关的任务。
- 使用 FIPS 140-2 第 2 级验证的 HSM。

[API 参考文档](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入门假设在 Windows 终端（例如 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 或 [Azure Cloud Shell](https://shell.azure.com/)）中运行 `dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和 Windows 命令。

## <a name="setting-up"></a>设置

### <a name="create-new-net-console-app"></a>创建新的 .NET 控制台应用

在控制台窗口中，使用 `dotnet new` 命令新建名为 `key-vault-console-app` 的 .NET 控制台应用。

```console
dotnet new console -n key-vault-console-app
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>安装包

在控制台窗口中，安装适用于 .NET 的 Azure Key Vault 客户端库：

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

本快速入门还需要安装以下包：

```console
dotnet add package Azure.Identity
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

使用适用于 .NET 的 Azure Key Vault 客户端库可以管理密钥和相关的资产（例如证书和机密）。 以下代码示例演示如何创建客户端以及设置、检索和删除机密。

[https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app ) 中提供了整个控制台应用。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

向密钥保管库进行身份验证和创建密钥保管库客户端，依赖于上面[设置环境变量](#set-environmental-variables)步骤中的环境变量。 密钥保管库的名称将扩展为密钥保管库 URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 下面的代码使用[“DefaultAzureCredential()”](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet)向密钥保管库进行身份验证，该密钥保管库会读取环境变量来检索访问令牌。 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>保存机密

应用程序通过身份验证后，你可以使用 [client.SetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)将机密放入密钥保管库。此操作需要使用机密的名称，本示例中使用“mySecret”。  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>检索机密

现在，可以使用 [client.GetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)检索以前设置的值。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

机密现已保存为 `secret.Value`。

### <a name="delete-a-secret"></a>删除机密

最后，使用 [client.DeleteSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)从密钥保管库中删除机密。

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令来验证是否已删除机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

### <a name="delete-a-key-vault"></a>删除 Key Vault
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>清除 Key Vault
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>删除资源组

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>示例代码

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault、存储了一个机密，然后检索了该机密。 请查看 [GitHub 中的整个控制台应用](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)。

若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
