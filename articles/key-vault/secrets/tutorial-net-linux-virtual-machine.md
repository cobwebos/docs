---
title: 教程 - 使用 Linux 虚拟机和 ASP.NET 控制台应用程序在 Azure Key Vault 中存储机密 | Microsoft Docs
description: 本教程介绍如何配置一个 ASP.NET Core 应用程序，以便从 Azure Key Vault 读取机密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81426166"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>教程：使用 Linux VM 和 .NET 应用在 Azure Key Vault 中存储机密

Azure Key Vault 用于保护机密，例如访问应用程序、服务和 IT 资源所需的 API 密钥和数据库连接字符串。

在本教程中，请设置 .NET 控制台应用程序以使用 Azure 资源的托管标识从 Azure Key Vault 读取信息。 学习如何：

> [!div class="checklist"]
> * 创建 key vault
> * 在 Key Vault 中存储机密
> * 创建一个 Azure Linux 虚拟机
> * 为虚拟机启用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
> * 授予所需的权限，让控制台应用程序从 Key Vault 读取数据
> * 从 Key Vault 检索机密

在我们进一步讨论之前，请阅读有关[密钥保管库基本概念](../general/basic-concepts.md)的内容。

## <a name="prerequisites"></a>先决条件

* [Git](https://git-scm.com/downloads)。
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure CLI 2.0 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)或 Azure Cloud Shell。

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>了解托管服务标识

Azure Key Vault 可以安全地存储凭据，因此不需将凭据置于代码中，但若要检索这些凭据，需向 Azure Key Vault 进行身份验证。 但是，若要向 Key Vault 进行身份验证，需提供凭据。 这是经典的启动问题。 有了 Azure 和 Azure Active Directory (Azure AD)，托管服务标识 (MSI) 就可以提供一个“启动标识”来大大简化启动过程。

为 Azure 服务（例如虚拟机、应用服务或 Functions）启用 MSI 时，Azure 会为 Azure Active Directory 中的服务实例创建一个服务主体。 它会将服务主体的凭据注入服务的实例。

![MSI](../media/MSI.png)

接下来，代码会调用 Azure 资源上提供的本地元数据服务，以获取访问令牌。
代码使用从本地 MSI_ENDPOINT 获取的访问令牌，以便向 Azure Key Vault 服务进行身份验证。

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在“美国西部”位置创建一个资源组。 在以下示例中，为资源组选取一个名称并用其替换 `YourResourceGroupName`：

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

本教程通篇使用此资源组。

## <a name="create-a-key-vault"></a>创建 key vault

接下来，在资源组中创建密钥保管库。 提供以下信息：

* 密钥保管库名称：由 3 到 24 个字符构成的字符串，只能包含数字、字母和连字符（0-9、a-z、A-Z 和 \-）。
* 资源组名称
* 位置：**美国西部**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-the-key-vault"></a>向密钥保管库添加机密

现在，请添加机密。 在实际方案中，在存储 SQL 连接字符串或任何其他信息时，可能既需要确保其安全，但同时也要确保其可供应用程序使用。

在本教程中，请键入以下命令，在密钥保管库中创建机密。 该机密名为 **AppSecret**，其值为 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>创建 Linux 虚拟机

使用 `az vm create` 命令创建 VM。

以下示例创建一个名为 **myVM** 的 VM 并添加一个名为 **azureuser** 的用户帐户。 `--generate-ssh-keys` 参数用来自动生成一个 SSH 密钥，并将其放置在默认密钥位置 ( **~/.ssh**) 中。 若要改为使用一组特定的密钥，请使用 `--ssh-key-value` 选项。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。 以下示例输出表明 VM 创建操作已成功。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

记下 VM 输出中的 `publicIpAddress`。 在后续步骤中，将使用此地址访问 VM。

## <a name="assign-an-identity-to-the-vm"></a>为 VM 分配标识

为虚拟机创建一个系统分配标识，方法是运行以下命令：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

此命令的输出应该是：

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

记下 `systemAssignedIdentity`。 在下一步使用它。

## <a name="give-the-vm-identity-permission-to-key-vault"></a>为 VM 标识提供 Key Vault 访问权限

现在可以向所创建的标识提供 Key Vault 权限。 运行以下命令：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>登录 VM

使用终端登录虚拟机。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>在 Linux 上安装 .NET Core

在 Linux VM 上：

运行以下命令，将 Microsoft 产品密钥注册为受信任的密钥：

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

根据操作系统设置所需版本主机包源：

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

安装 .NET 并检查版本：

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>创建并运行示例 .NET 应用

运行以下命令。 此时会看到“Hello World”输出到控制台。

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>编辑用于提取机密的控制台应用

打开 Program.cs 文件，添加以下包：

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

需要通过一个两步过程更改类文件，使应用能够访问密钥保管库中的机密。

1. 从 VM 上的本地 MSI 终结点提取一个令牌，该终结点会转而从 Azure Active Directory 提取令牌。
1. 将令牌传递到 Key Vault，提取机密。

   编辑类文件，使之包含以下代码：

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

现在，我们已学习了如何在 Azure Linux 虚拟机运行的 .NET 应用程序中通过 Azure Key Vault 执行操作。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请选择适用于 VM 的资源组，然后选择“删除”。 

使用 `az keyvault delete` 命令删除密钥保管库：

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
