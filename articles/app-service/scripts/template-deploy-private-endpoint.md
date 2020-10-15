---
title: 使用 Azure 资源管理器模板为 Web 应用部署专用终结点
description: 了解如何使用 ARM 模板为 Web 应用部署专用终结点。
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652019"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建应用服务应用并部署专用终结点

在本快速入门中，使用 Azure 资源管理器 (ARM) 模板创建 Web 应用，并使用专用终结点公开该 Web 应用。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>先决条件

需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-private-endpoint"></a>创建专用终结点

该模板为 Azure Web 应用创建专用终结点。

### <a name="review-the-template"></a>查看模板

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>部署模板

下面介绍如何将 Azure 资源管理器模板部署到 Azure：

1. 若要登录到 Azure 并打开模板，请选择此链接：[部署到 Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)。 使用该模板可创建虚拟网络、Web 应用、专用终结点和专用 DNS 区域。
2. 选择或创建资源组。
3. 输入 Web 应用、Azure 应用服务计划、专用终结点的名称。
5. 阅读有关条款和条件的声明。 如果你同意，请选择“我同意上述条款和条件” > “购买” 。 部署可能需要几分钟才能完成。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为专用终结点创建的资源，请删除资源组。 这会删除该专用终结点和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

- 可以在 [ARM 模板示例](../samples-resource-manager-templates.md)中找到 Azure 应用服务 Web 应用的更多 Azure 资源管理器模板。
