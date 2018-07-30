---
title: Azure 快速入门 - 将 Azure Web 应用程序配置为从 Key Vault 读取机密 | Microsoft Docs
description: 介绍如何将 ASP.Net Core 应用程序配置为从 Key Vault 读取机密的快速入门
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247819"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>快速入门：在 .NET Web 应用中通过 Key Vault 设置和读取机密

本快速入门演练将 Azure Web 应用程序配置为使用托管服务标识从 Key Vault 读取信息所要执行的步骤。 学习如何：

> [!div class="checklist"]
> * 创建 Key Vault。
> * 在 Key Vault 中存储机密。
> * 从 Key Vault 检索机密。
> * 创建 Azure Web 应用程序。
> * [启用托管服务标识](../active-directory/managed-service-identity/overview.md)。
> * 授予所需的权限，让 Web 应用程序从 Key Vault 读取数据。

在继续下一步之前，请阅读[基本概念](key-vault-whatis.md#basic-concepts)，尤其是[托管服务标识](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>先决条件

* 在 Windows 上：
  * [Visual Studio 2017 15.7.3 或更高版本](https://www.microsoft.com/net/download/windows)，其中包含以下工作负荷：
    * ASP.NET 和 Web 开发
    * .NET Core 跨平台开发
  * [.NET Core 2.1 SDK 或更高版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * https://visualstudio.microsoft.com/vs/mac/

* 所有平台：
  * 从[此处](https://git-scm.com/downloads)下载 GIT。
  * Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)：需要 Azure CLI 2.0.4 或更高版本。 适用于 Windows、Mac 和 Linux

## <a name="login-to-azure"></a>登录到 Azure

   若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

请选择一个资源组名称，然后将其填充在占位符中。
以下示例在 *eastus* 位置创建名为 *<YourResourceGroupName>* 的资源组。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

刚刚创建的资源组将在整篇文章中使用。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

接下来，在上一步骤创建的资源组中创建 Key Vault。 提供以下信息：

* 保管库名称 - **请在此处选择一个密钥保管库名称**。 Key Vault 名称必须是长度为 3-24 个字符，且仅包含 0-9、a-z、A-Z 和 - 的字符串。
* 资源组名称 - **请在此处选择一个资源组名称**。
* 位置 -“美国东部”。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

我们将添加机密以帮助说明这是如何工作的。 可以存储需要安全保存的，但同时也要提供给应用程序使用的 SQL 连接字符串或其他任何信息。 在本教程中，密码名为 **AppSecret**，将在其中存储 **MySecret** 的值。

键入以下命令，在 Key Vault 中创建名为 **AppSecret** 的机密，用于存储 **MySecret** 值：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令显示机密信息，包括 URI。 完成这些步骤后，Azure Key Vault 中应会出现某个机密的 URI。 请记下此信息， 后面的步骤需要用到。

## <a name="clone-the-repo"></a>克隆存储库

运行以下命令，以便克隆存储库，为你创建一个用于编辑源的本地副本：

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>打开并编辑解决方案

编辑 program.cs 文件，以便使用特定的密钥保管库名称运行示例。

1. 导航到 key-vault-dotnet-core-quickstart 文件夹
2. 在 Visual Studio 2017 中打开 key-vault-dotnet-core-quickstart.sln 文件
3. 打开 Program.cs 文件，将占位符 <YourKeyVaultName> 更新为以前创建的 Key Vault 的名称。

此解决方案使用 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 库

## <a name="run-the-app"></a>运行应用

在 Visual Studio 2017 的主菜单中，选择“调试”>“开始执行(不调试)”。 显示浏览器时，导航到“关于”页。 此时将显示 AppSecret 的值。

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

我们要将此应用发布到 Azure，看其是否可以作为 Web 应用运行，并看我们能否提取机密值

1. 在 Visual Studio 中选择 **key-vault-dotnet-core-quickstart** 项目。
2. 依次选择“发布”、“启动”。
3. 创建新的**应用服务**，选择“发布”。
4. 将“应用名称”更改为“keyvaultdotnetcorequickstart”。
5. 选择**创建**。

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>启用托管服务标识 (MSI)

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Azure Key Vault 的身份验证才能检索它们。 托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

1. 返回 Azure CLI
2. 运行 assign-identity 命令，为此应用程序创建标识：

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>此命令等同于转到门户并在 Web 应用程序属性中将“托管服务标识”切换为“打开”。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>为应用程序分配从 Key Vault 读取机密的权限

请记下[将应用程序发布到 Azure][] 时的输出。 它应该采用以下格式：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然后，使用从上面复制的 Key Vault 名称和 PrincipalId 值运行以下命令：

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>后续步骤

* [Azure Key Vault 主页](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 文档](https://docs.microsoft.com/azure/key-vault/)
* [用于 .NET 的 Azure SDK](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API 参考](https://docs.microsoft.com/rest/api/keyvault/)
