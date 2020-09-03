---
title: 教程 - 将 Azure Key Vault 与通过 .NET 编写的虚拟机配合使用 | Microsoft Docs
description: 本教程介绍如何将 ASP.NET Core 应用程序配置为从 Key Vault 读取机密。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b4f832750f7a94a6a60cbb1d3ba630925dd4fff2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021725"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>教程：将 Azure Key Vault 与通过 .NET 编写的虚拟机配合使用

Azure Key Vault 可以帮助保护机密，例如访问应用程序、服务和 IT 资源时所需的 API 密钥与数据库连接字符串。

本教程介绍如何获取控制台应用程序，以便从 Azure Key Vault 读取信息。 应用程序将使用虚拟机托管标识对 Key Vault 进行身份验证。 

本教程介绍如何：

> [!div class="checklist"]
> * 创建资源组。
> * 创建密钥保管库。
> * 将机密添加到 Key Vault。
> * 从密钥保管库检索机密。
> * 创建一个 Azure 虚拟机。
> * 为虚拟机启用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
> * 为 VM 标识分配权限。

在开始之前，请阅读 [Key Vault 的基本概念](basic-concepts.md)。 

如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

对于 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-resources-and-assign-permissions"></a>创建资源并分配权限

在开始编码之前，需要创建一些资源，将机密放入密钥保管库，并分配权限。

### <a name="sign-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>使用机密填充密钥保管库

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>创建虚拟机
使用以下方法之一创建 Windows 或 Linux 虚拟机：

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure 门户](../../virtual-machines/windows/quick-create-portal.md) | [Azure 门户](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>为 VM 分配标识
使用 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令为虚拟机创建系统分配的标识：

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
使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令将以前创建的标识权限分配给密钥保管库：

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>登录到虚拟机

若要登录到虚拟机，请按照[连接并登录到 Azure Windows 虚拟机](../../virtual-machines/windows/connect-logon.md)或[连接并登录到 Azure Linux 虚拟机](../../virtual-machines/linux/login-using-aad.md)中的说明操作。

## <a name="set-up-the-console-app"></a>设置控制台应用

创建控制台应用并使用 `dotnet` 命令安装所需的包。

### <a name="install-net-core"></a>安装 .NET Core

若要安装 .NET Core，请转到 [.NET 下载](https://www.microsoft.com/net/download)页。

### <a name="create-and-run-a-sample-net-app"></a>创建并运行示例 .NET 应用

打开命令提示符。

可以运行以下命令，将“Hello World”输出到控制台：

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>安装包

在控制台窗口中，安装适用于 .NET 的 Azure Key Vault 机密客户端库：

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

对于本快速入门，你将需要安装以下标识包，以对 Azure Key Vault 进行身份验证：

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>编辑控制台应用

打开 *Program.cs* 文件，添加以下包：

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

添加以下行，更新 URI 以反映密钥保管库的 `vaultUri`。 下面的代码将 [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) 用于向密钥保管库进行身份验证，该类使用来自应用程序托管标识的令牌进行身份验证。 它还在密钥保管库受到限制的情况下将指数退避用于重试。

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

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
```

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的虚拟机和 Key Vault 时，请将其删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
