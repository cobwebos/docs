---
title: “将 Azure Web 应用程序配置为从 Key Vault 读取机密”教程 | Microsoft Docs
description: 教程：将 Node.js 应用程序配置为从 Key Vault 读取机密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: cc43081463667eba06af6538f3d78f16544ed2a5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412236"
---
# <a name="quickstart-how-to-set-and-read-a-secret-from-key-vault-in-a-node-web-app"></a>快速入门：如何在 Node Web 应用中通过 Key Vault 设置和读取机密 

本快速入门介绍如何在 Key Vault 中存储机密，以及如何使用 Web 应用来检索它。 此 Web 应用可以在本地或 Azure 中运行。 本快速入门使用 Node.js 和托管服务标识 (MSI)

> [!div class="checklist"]
> * 创建 Key Vault。
> * 在 Key Vault 中存储机密。
> * 从 Key Vault 检索机密。
> * 创建 Azure Web 应用程序。
> * [启用托管服务标识](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。
> * 授予所需的权限，让 Web 应用程序从 Key Vault 读取数据。

在继续操作之前，请确保熟悉[基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>先决条件

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更高版本
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="login-to-azure"></a>登录到 Azure

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

## <a name="create-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

请选择一个资源组名称，然后将其填充在占位符中。
以下示例在 *eastus* 位置创建名为 *<YourResourceGroupName>* 的资源组。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

刚刚创建的资源组将在整篇教程中使用。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

接下来，在上一步创建的资源组中创建 Key Vault。 尽管本文中使用“ContosoKeyVault”作为密钥保管库的名称，但你必须使用唯一名称。 提供以下信息：

* 保管库名称 - **在此处选择一个密钥保管库名称**。
* 资源组名称 - **在此处选择一个资源组名称**。
* 位置 -“美国东部”。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-key-vault"></a>向密钥保管库添加机密

我们将添加机密以帮助说明这是如何工作的。 可以存储需要安全保存的，但同时也要提供给应用程序使用的 SQL 连接字符串或其他任何信息。 在本教程中，密码名为 **AppSecret**，将在其中存储 **MySecret** 的值。

键入以下命令，在 Key Vault 中创建名为 **AppSecret** 的机密，用于存储 **MySecret** 值：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令显示机密信息，包括 URI。 完成这些步骤后，Azure Key Vault 中应会出现某个机密的 URI。 请记下此信息。 后面的步骤需要用到。

## <a name="clone-the-repo"></a>克隆存储库

运行以下命令，以便克隆存储库，为你创建一个用于编辑源的本地副本：

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>安装依赖项

在这里，我们安装依赖项。 运行以下命令：cd key-vault-node-quickstart npm install

此项目使用 2 个节点模块：

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

下面是需完成的一些步骤

- 第一步是创建 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)计划。 可以在此计划中存储多个 Web 应用。

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- 接下来创建 Web 应用。 在以下示例中，将 <app_name> 替换为全局唯一的应用名称（有效字符为 a-z、0-9 和 -）。 运行时设置为 NODE|6.9。 若要查看所有受支持的运行时，请运行 az webapp list-runtimes
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    # PowerShell
    az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
    ```
    创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    浏览到新建的 Web 应用，此时会看到正在运行的 Web 应用。 将 <app_name> 替换为唯一的应用名称。

    ```
    http://<app name>.azurewebsites.net
    ```
    以上命令也创建一个支持 Git 的应用，该应用可以用来将内容从本地 Git 部署到 Azure。 
    本地 Git 配置了 URL“https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git”

- 创建部署用户：在完成上一命令以后，可以向本地 Git 存储库添加 Azure 远程功能。 使用从“启用应用的 Git”中获取的 Git 远程 URL 替换 <url>。

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>启用托管服务标识

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

运行 assign-identity 命令，为此应用程序创建标识：

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

此命令等同于转到门户并在 Web 应用程序属性中将“托管服务标识”切换为“打开”。

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>为应用程序分配从 Key Vault 读取机密的权限

记下或复制上面的命令的输出。 它应采用以下格式：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然后，使用从上面复制的 Key Vault 名称和 PrincipalId 值运行以下命令：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>将 Node 应用部署到 Azure，然后检索机密值

现在，一切都已设置好。 运行以下命令，将应用部署到 Azure

```
git push azure master
```

然后，在浏览 https://<app_name>.azurewebsites.net 时，即可看到机密值。
确保已将名称 <YourKeyVaultName> 替换为保管库名称

## <a name="next-steps"></a>后续步骤

* [Azure Key Vault 主页](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 文档](https://docs.microsoft.com/azure/key-vault/)
* [用于 Node 的 Azure SDK](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API 参考](https://docs.microsoft.com/rest/api/keyvault/)