---
title: 快速入门 - 使用 Azure CLI 创建 Azure 通知中心 | Microsoft Docs
description: 本教程介绍如何使用 Azure CLI 创建 Azure 通知中心。
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069480"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 通知中心

Azure 通知中心提供易于使用且横向扩展的推送引擎，可用于从任何后端（云或本地）向任何平台（iOS、Android、Windows、Kindle、百度等）发送通知。 有关此服务的详细信息，请参阅[什么是 Azure 通知中心？](notification-hubs-push-notification-overview.md)。

在本快速入门中，你将使用 Azure CLI 创建通知中心。 第一部分提供创建通知中心命名空间，以及查询该命名空间的访问策略信息的步骤。 第二部分提供在现有命名空间中创建通知中心的步骤。  此外，介绍如何创建自定义访问策略。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

通知中心需要 Azure CLI 2.0.67 或更高版本。 运行 `az --version` 查看安装的版本和依赖库。 若要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prepare-your-environment"></a>准备环境

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录。

    ```azurecli-interactive
    az login
    ```

    遵循终端中显示的步骤完成身份验证过程。

2. 安装 Azure CLI 扩展。

   若要针对通知中心运行 Azure CLI 命令，请安装[适用于通知中心的 Azure CLI 扩展](/cli/azure/ext/notification-hub/notification-hub)。  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. 创建资源组。

   与所有 Azure 资源一样，Azure 通知中心必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

   对于本快速入门，请使用以下 [az group create](/cli/azure/group#az-group-create) 命令在 *eastus* 位置创建名为 *spnhubrg* 的资源组：

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>创建通知中心命名空间

1. 创建通知中心的命名空间

   命名空间包含一个或多个中心，其名称在所有 Azure 订阅之间必须保持唯一。  若要检查给定的服务命名空间是否可用，请使用 [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) 命令。  运行 [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) 命令创建命名空间。  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. 列出命名空间访问策略的密钥和连接字符串。

   系统将为新命名空间自动创建名为 **RootManageSharedAccessKey** 的访问策略。  每个访问策略有两组密钥和连接字符串。  若要列出命名空间的密钥和连接字符串，请运行 [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 命令。

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>创建通知中心

1. 创建第一个通知中心。

   现在，可在新的命名空间中创建通知中心。  运行 [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) 命令创建通知中心。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 创建另一个通知中心。

   可以在一个命名空间中创建多个通知中心。  若要在同一个命名空间中创建另一个通知中心，请使用不同的中心名称再次运行 `az notification-hub create` 命令。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>使用访问策略

1. 为通知中心创建新的授权规则。

   系统将为每个新通知中心自动创建访问策略。  若要创建和自定义自己的访问策略，请使用 [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) 命令。

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. 列出通知中心的访问策略。

   若要查询通知中心存在哪些访问策略，请使用 [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) 命令。

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 请不要在应用程序中使用 **DefaultFullSharedAccessSignature** 策略。 这只能在后端使用。  在客户端应用程序中请仅使用 **Listen** 访问策略。

3. 列出通知中心访问策略的密钥和连接字符串

   每个访问策略有两组密钥和连接字符串。  稍后在处理推送通知时需要它们。  若要列出通知中心访问策略的密钥和连接字符串，请使用 [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 命令。

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [通知中心命名空间](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)和[通知中心](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)具有不同的访问策略。  查询密钥和连接字符串时，请确保使用正确的 Azure CLI 参考内容。

## <a name="clean-up-resources"></a>清理资源

不再需要上述资源组和所有相关资源时，请使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>另请参阅

了解用于通过 Azure CLI 管理通知中心的全部功能。

* [通知中心的完整 Azure CLI 参考列表](/cli/azure/ext/notification-hub/notification-hub)
* [通知中心命名空间的 Azure CLI 参考列表](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [通知中心授权规则的 Azure CLI 参考列表](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [通知中心凭据的 Azure CLI 参考列表](/cli/azure/ext/notification-hub/notification-hub/credential)
