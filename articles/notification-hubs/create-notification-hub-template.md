---
title: 使用 Azure 资源管理器模板创建 Azure 通知中心
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 通知中心。
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 08/04/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: 2165351bee4ee260e768c10b23e26bf095779cad
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684637"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>快速入门：使用 ARM 模板创建通知中心

Azure 通知中心提供易于使用且横向扩展的推送引擎，允许你从任何后端（云或本地）向任何平台（iOS、Android、Windows、Kindle 等）发送通知。 有关此服务的详细信息，请参阅[什么是 Azure 通知中心](notification-hubs-push-notification-overview.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

本快速入门使用 Azure 资源管理器模板（ARM 模板）来创建 Azure 通知中心命名空间，并在命名空间中创建名为“MyHub”的通知中心。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-notification-hub/)。

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>部署模板

选择下图登录到 Azure 并打开一个模板。 模板采用通知中心命名空间名称作为参数。 然后，该模板创建一个具有该名称的命名空间，并在该命名空间中创建名为“MyHub”的通知中心。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 脚本列出已部署的通知中心命名空间和中心：

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
:::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::: null
---

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除资源组中的资源。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
