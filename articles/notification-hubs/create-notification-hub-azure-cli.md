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
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5361931328ed107c7cc130b633a40b1582828aa1
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024109"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure 通知中心

Azure 通知中心提供易于使用且横向扩展的推送引擎，可用于从任何后端（云或本地）向任何平台（iOS、Android、Windows、Kindle、百度等）发送通知。 有关此服务的详细信息，请参阅[什么是 Azure 通知中心？](notification-hubs-push-notification-overview.md)。

在本快速入门中，你将使用 Azure CLI 创建通知中心。 第一部分提供创建通知中心命名空间的步骤。 第二部分提供在现有命名空间中创建通知中心的步骤。 此外，介绍如何创建自定义访问策略。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> 通知中心需要 Azure CLI 2.0.67 或更高版本。 运行 `az --version` 查看安装的版本和依赖库。 若要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用 Azure CLI 的扩展引用时，必须先安装该扩展。 借助 Azure CLI 扩展，可访问尚未在核心 CLI 中提供的试验性的和预发布的命令。 若要详细了解包含更新和卸载的扩展，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。

安装用于通知中心的 Azure CLI 扩展。

```azurecli
az extension add --name notification-hub
```

## <a name="create-a-resource-group"></a>创建资源组

与所有 Azure 资源一样，Azure 通知中心必须部署到资源组中。  使用资源组可以组织和管理相关的 Azure 资源。  请参阅[什么是 Azure 资源管理器](/azure/azure-resource-manager/management/overview)以详细了解资源组。

对于本快速入门，请使用以下 [az group create](/cli/azure/group#az-group-create) 命令在“eastus”位置创建名为“spnhubrg”的资源组。

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>创建通知中心命名空间

1. 创建通知中心的命名空间。

   命名空间包含一个或多个中心，其名称在所有 Azure 订阅上必须保持唯一且长度至少为六个字符。 若要检查名称是否可用，请使用 [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) 命令。

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI 通过显示以下控制台输出来响应你的可用性请求：

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   请注意 Azure CLI 响应中的第二行 `"isAvailable": true`。 如果为命名空间指定的所需名称不可用，此行将读取 `false`。 确认名称的可用性后，请运行 [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) 命令来创建命名空间。  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   如果提供给 `az notification-hub namespace create` 命令的 `--name` 不可用，或不满足 [Azure 资源的命名规则和限制](../azure-resource-manager/management/resource-name-rules.md)，Azure CLI 将使用以下控制台输出进行响应：

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   如果尝试的第一个名称不成功，请为新命名空间选择其他名称，然后再次运行 `az notification-hub namespace create` 命令。

   > [!NOTE]
   > 在此步骤中，必须在从此快速入门中复制的每个 Azure CLI 命令中替换 `--namespace` 参数的值。

2. 获取命名空间的列表。

   若要查看有关新命名空间的详细信息，请使用 [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) 命令。 如果要查看订阅的所有命名空间，则 `--resource-group` 参数为可选。

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>创建通知中心

1. 创建第一个通知中心。

   现在，可在新的命名空间中创建一个或多个通知中心。 运行 [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) 命令创建通知中心。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 创建另一个通知中心。

   可以在一个命名空间中创建多个通知中心。 若要在同一个命名空间中创建另一个通知中心，请使用不同的中心名称再次运行 `az notification-hub create` 命令。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. 获取通知中心的列表。

   Azure CLI 会在执行每个命令后返回成功或错误消息；不过你一定能够查询通知中心的列表。 [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) 命令旨在实现此目的。

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>使用访问策略

1. Azure 通知中心通过使用访问策略来获得[共享访问签名安全性](./notification-hubs-push-notification-security.md)。 创建通知中心时，会自动创建两个策略。 需要这些策略中的连接字符串来配置推送通知。 [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) 命令提供策略名称及其各自资源组的列表。

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 请不要在应用程序中使用 _DefaultFullSharedAccessSignature_ 策略。 此策略只能在后端使用。 请仅在客户端应用程序中使用 `Listen` 访问策略。

2. 如果要使用有意义的名称创建其他授权规则，可使用 [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) 命令来创建和自定义你自己的访问策略。 `--rights` 参数是要分配的权限的空格分隔列表。

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. 每个访问策略有两组密钥和连接字符串。 稍后在[配置通知中心](./configure-notification-hub-portal-pns-settings.md)时需要它们。 若要列出通知中心访问策略的密钥和连接字符串，请使用 [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 命令。

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [通知中心命名空间](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)和[通知中心](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)具有不同的访问策略。 查询密钥和连接字符串时，请确保使用正确的 Azure CLI 参考内容。

## <a name="clean-up-resources"></a>清理资源

不再需要上述资源组和所有相关资源时，请使用 [az group delete](/cli/azure/group) 命令将其删除：

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>后续步骤

* 在本快速入门中，你创建了一个通知中心。 若要了解如何使用平台通知系统 (PNS) 设置来配置通知中心，请参阅[在通知中心设置推送通知](configure-notification-hub-portal-pns-settings.md)

* 了解大量用于通过 Azure CLI 管理通知中心的功能：

  [通知中心的完整参考列表](/cli/azure/ext/notification-hub/notification-hub)

  [通知中心命名空间的参考列表](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [通知中心授权规则的参考列表](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [通知中心凭据的参考列表](/cli/azure/ext/notification-hub/notification-hub/credential)
