---
title: 快速入门 - 适用于 .NET 的 Azure Key Vault 客户端库
description: 提供编写 Azure SDK 客户端库快速入门时要遵循的格式和内容准则。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c67b24d57117a248559424497939a04ce347658c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308943"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>快速入门：适用于 .NET 的 Azure Key Vault 客户端库

适用于 .NET 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 使用适用于 .NET 的 Key Vault 客户端库可以：

- 提高安全性以及控制密钥和密码。
- 在几分钟内创建并导入加密密钥。
- 通过云扩展和全局冗余减少延迟。
- 简化和自动化与 SSL/TLS 证书相关的任务。
- 使用 FIPS 140-2 第 2 级验证的 HSM。

[API 参考文档](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)


## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 2.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/overview)

本快速入门假设在 Windows 终端（例如 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 或 [Azure Cloud Shell](https://shell.azure.com/)）中运行 `dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和 Windows 命令。

## <a name="setting-up"></a>设置

### <a name="create-new-net-console-app"></a>创建新的 .NET 控制台应用

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。

在控制台窗口中，使用 `dotnet new` 命令创建名为 `akv-dotnet` 的新控制台应用。


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

本快速入门还需要安装以下包：

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](quick-create-cli.md)、[Azure PowerShell 快速入门](quick-create-powershell.md)或 [Azure 门户快速入门](quick-create-portal.md)中的步骤创建 Key Vault。 或者，只需运行以下 Azure CLI 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 以下示例创建名为 *myKV* 的 Key Vault，但用户必须指定不同的名称，并在整个快速入门中使用指定的名称。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>创建服务主体

对基于云的 .NET 应用程序进行身份验证的最简单方法是使用托管标识；有关详细信息，请参阅[使用 .NET 对 Azure Key Vault 进行服务到服务的身份验证](service-to-service-authentication.md)。 不过，为简单起见，本快速入门将创建一个 .NET 控制台应用程序。 在 Azure 中对桌面应用程序进行身份验证需要使用服务主体。
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

请记下 clientId、clientSecret、subscriptionId 和 tenantId，因为在下面的[对 Key Vault 进行身份验证](#authenticate-to-your-key-vault)步骤中将要用到。

此外还需要服务主体的 appID。 可以结合 `--show-mine` 参数运行 [az ad sp list](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 找到该 ID：

```azurecli
az ad sp list --show-mine
```

`appID` 将显示在返回的 JSON 中：

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

针对 Key Vault 创建一个访问策略，以便为服务主体授予权限。 可以使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令实现此目的。 我们将为服务主体授予对密钥和机密的 get、list 和 set 权限。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>对象模型

使用适用于 .NET 的 Azure Key Vault 客户端库可以管理密钥和相关的资产（例如证书和机密）。 以下代码示例演示如何设置机密和检索机密。

https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet 中提供了整个控制台应用。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>对 Key Vault 进行身份验证

本 .NET 快速入门依赖于使用环境变量来存储不应放入、但需要放入代码中的凭据。 

在生成和运行应用之前，请使用 `setx` 命令将 `akvClientId`、`akvClientSecret`、`akvTenantId` 和 `akvSubscriptionId` 环境变量设置为上面记下的值。

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>

setx akvTenantId <your-tentantId>

setx akvSubscriptionId <your-subscriptionId>
````

每次调用 `setx` 时，都应会收到响应“成功:已保存指定的值。”

将这些环境变量分配到代码中的字符串，然后通过将这些字符串传递给 [KeyVaultClient 类](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)，来对应用程序进行身份验证：

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>保存机密

对应用程序进行身份验证后，可以使用 [SetSecretAsync 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)将机密放入 Key Vault。这需要使用 Key Vault 的 URL，其格式为 `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`。 还需要使用机密的名称 - 我们使用了“mySecret”。  可将这些字符串分配到某个变量，以便于重复使用。

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>检索机密

现在，可以使用 [GetSecretAsync 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)检索以前设置的值

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

机密现已保存为 `keyvaultSecret.Value;`。

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault、存储了一个机密，然后检索了该机密。 请查看 [GitHub 中的整个控制台应用](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)。

若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 实现[使用 .NET 对 Azure Key Vault 进行服务到服务的身份验证](service-to-service-authentication.md)
- 阅读 [Azure Key Vault 概述](key-vault-overview.md)
- 参阅 [Azure Key Vault 开发人员指南](key-vault-developers-guide.md)
- 了解[密钥、机密和证书](about-keys-secrets-and-certificates.md)
- 查看 [Azure Key Vault 最佳做法](key-vault-best-practices.md)
