---
title: 配置 Azure 应用程序配置的托管标识
description: 了解托管标识在 Azure 应用程序配置中的工作原理以及如何配置托管标识
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 270f8d471a95e67939a31357bc1cc056fe8ea4a1
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072792"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>如何将托管标识用于 Azure 应用程序配置

本主题介绍如何为 Azure 应用程序配置创建托管标识。 借助 Azure Active Directory (AAD) 的托管标识，Azure 应用程序配置可以轻松访问其他受 AAD 保护的资源（如 Azure Key Vault）。 标识由 Azure 平台管理。 无需预配或轮换任何机密。 有关 AAD 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识：

- 配置存储区绑定了一个系统分配的标识。 如果删除配置存储区，则标识将一并删除。 一个配置存储区只能有一个系统分配的标识。
- 用户分配的标识是可以分配给配置存储区的独立 Azure 资源。 一个配置存储区可以有多个用户分配的标识。

## <a name="adding-a-system-assigned-identity"></a>添加系统分配的标识

若要创建带有系统分配的标识的应用程序配置存储区，需要在该存储区上设置一个额外的属性。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 设置托管标识，请对现有配置存储区使用 [az appconfig identity assign] 命令。 运行本部分中的示例有三个选项：

- 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 通过单击下面每个代码块右上角的“试用”按钮，来使用嵌入的 Azure Cloud Shell。
- 如果想要使用本地 CLI 控制台，请[安装最新版 Azure CLI](/cli/azure/install-azure-cli)（2.1 或更高版本）。

以下步骤将指导你完成使用 CLI 创建应用程序配置存储区并为其分配标识的操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login] 登录到 Azure。 请使用与 Azure 订阅关联的帐户：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 创建应用程序配置存储区。 有关如何将 CLI 与 Azure 应用程序配置结合使用的更多示例，请参阅[应用程序配置 CLI 示例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 运行 [az appconfig identity assign] 命令，为此配置存储区创建系统分配的标识：

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>添加用户分配的标识

若要创建带有用户分配的标识的应用程序配置存储区，需要先创建该标识，然后将其资源标识符分配到你的存储区。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 设置托管标识，请对现有配置存储区使用 [az appconfig identity assign] 命令。 运行本部分中的示例有三个选项：

- 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 通过单击下面每个代码块右上角的“试用”按钮，来使用嵌入的 Azure Cloud Shell。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 Azure CLI](/cli/azure/install-azure-cli)（2.0.31 或更高版本）。

以下步骤演示如何创建用户分配的标识和应用程序配置存储区，然后使用 CLI 将该标识分配给存储：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login] 登录到 Azure。 请使用与 Azure 订阅关联的帐户：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 创建应用程序配置存储区。 有关如何将 CLI 与 Azure 应用程序配置结合使用的更多示例，请参阅[应用程序配置 CLI 示例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 使用 CLI 创建名为 `myUserAssignedIdentity` 的用户分配的标识。

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    记下此命令的输出中 `id` 属性的值。

1. 运行 [az appconfig identity assign] 命令，向此配置存储区分配新的用户分配的标识。 使用在上一步中记下的 `id` 属性的值。

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>删除标识

可通过使用 Azure CLI 中的 [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) 命令禁用此功能来删除系统分配的标识。 可以单独删除用户分配的标识。 以这种方式删除系统分配的标识也会将它从 AAD 中删除。 删除应用资源时，也将自动从 AAD 中删除系统分配的标识。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure 应用配置创建 ASP.NET Core 应用](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login