---
title: 快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密 - Azure Key Vault | Microsoft Docs
description: 在本快速入门中，请使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4f9fff41e4b9043c271d656583fb8b9a11ff3a7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052781"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密

本快速入门演练将 Azure Web 应用程序配置为使用 Azure 资源的托管标识，以从 Azure Key Vault 读取信息所要执行的步骤。 可以通过 Key Vault 来确保信息的安全。 学习如何：

* 创建密钥保管库。
* 在密钥保管库中存储机密。
* 从密钥保管库检索机密。
* 创建 Azure Web 应用程序。
* 为 Web 应用启用[托管服务标识](../active-directory/managed-identities-azure-resources/overview.md)。
* 授予所需的权限，让 Web 应用程序从密钥保管库读取数据。

在我们进一步讨论之前，请阅读 [Key Vault 基本概念](key-vault-whatis.md#basic-concepts)。

>[!NOTE]
>Key Vault 是一个以编程方式存储机密的中央存储库。 但要这样做，应用程序和用户需要首先向 Key Vault 进行身份验证，即提供机密。 为了遵循安全最佳做法，第一个机密需要定期轮换。 
>
>使用 [Azure 资源的托管服务标识](../active-directory/managed-identities-azure-resources/overview.md)，在 Azure 中运行的应用程序将获得由 Azure 自动管理的标识。 这有助于解决*机密采用问题*，这样用户和应用程序就可以遵循最佳做法，而不必担心轮换第一个机密。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

* 在 Windows 上：
  * 带有以下工作负荷的 [Visual Studio 2019](https://www.microsoft.com/net/download/windows)：
    * ASP.NET 和 Web 开发
    * .NET Core 跨平台开发
  * [.NET Core 2.1 SDK 或更高版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * 请参阅 [Visual Studio for Mac 中的新增功能](https://visualstudio.microsoft.com/vs/mac/)。

* 所有平台：
  * Git（[下载](https://git-scm.com/downloads)）。
  * Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更高版本。 适用于 Windows、Mac 和 Linux。

## <a name="log-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

选择一个资源组名称，然后将其填充在占位符中。
以下示例在“美国东部”位置创建一个资源组：

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

刚刚创建的资源组将在整篇文章中使用。

## <a name="create-a-key-vault"></a>创建 key vault

接下来，在上一步骤创建的资源组中创建密钥保管库。 提供以下信息：

* Key Vault 名称：名称必须是 3-24 个字符的字符串，并且只能包含 0-9、a-z、A-Z 和连字符 (-)。
* 资源组名称。
* 位置：**美国东部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-the-key-vault"></a>向密钥保管库添加机密

我们将添加机密以帮助说明这是如何工作的。 可以存储需要安全保存的，但同时也要提供给应用程序使用的 SQL 连接字符串或其他任何信息。

键入以下命令，在名为 **AppSecret** 的密钥保管库中创建机密。 此机密将存储值“MySecret”。 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令显示机密信息，包括 URI。 完成这些步骤后，密钥保管库中会出现某个机密的 URI。 请记下此信息， 后面的步骤需要用到。

## <a name="clone-the-repo"></a>克隆存储库

克隆存储库，以便创建一个可以在其中编辑源的本地副本。 运行以下命令：

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>打开并编辑解决方案

编辑 program.cs 文件，以便使用特定的密钥保管库名称运行示例：

1. 浏览到 key-vault-dotnet-core-quickstart 文件夹。
2. 在 Visual Studio 2019 中打开 key-vault-dotnet-core-quickstart.sln 文件。
3. 打开 Program.cs 文件，将占位符 *YourKeyVaultName* 更新为以前创建的密钥保管库的名称。

此解决方案使用 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 库。

## <a name="run-the-app"></a>运行应用

在 Visual Studio 2019 的主菜单中，选择“调试” > “开始执行(不调试)”。   显示浏览器时，转到“关于”页。  此时会显示 **AppSecret** 的值。

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

将此应用发布到 Azure，看其是否可以作为 Web 应用运行，并看能否提取机密值：

1. 在 Visual Studio 中选择 **key-vault-dotnet-core-quickstart** 项目。
2. 选择“发布”   >   “开始”。
3. 创建新的**应用服务**，然后选择“发布”  。
4. 将应用名称更改为“keyvaultdotnetcorequickstart”。 
5. 选择“创建”  。

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>为 Web 应用启用托管标识

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 [Azure 资源的托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

在 Azure CLI 中运行 assign-identity 命令，为此应用程序创建标识：

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>此过程中的命令等同于转到门户并在 Web 应用程序属性中将“标识/系统分配”设置切换为“打开”   。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>为应用程序分配从 Key Vault 读取机密的权限

请记下将应用程序发布到 Azure 时的输出。 它应该采用以下格式：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然后，使用密钥保管库名称和 **PrincipalId** 值运行以下命令：

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

现在运行应用程序时，会看到检索到的机密值。 上面的命令中向应用服务的标识提供了在密钥保管库上执行“get”和“list”操作的权限   。

## <a name="clean-up-resources"></a>清理资源
不再需要资源组、虚拟机和所有相关的资源时，可将其删除。 为此，请选择适用于密钥保管库的资源组，然后选择“删除”。 

使用 [az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete) 命令删除密钥保管库：

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [深入了解 Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
