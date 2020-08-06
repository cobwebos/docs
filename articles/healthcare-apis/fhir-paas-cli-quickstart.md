---
title: 快速入门：使用 Azure CLI 部署 Azure API for FHIR
description: 在本快速入门中，你将了解如何使用 Azure CLI 在 Azure 中部署 Azure API for FHIR。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: mihansen
ms.custom: devx-track-azurecli
ms.openlocfilehash: e2fdd4c4298946d943ee8a806ebc53966c8dd624
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500889"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>快速入门：使用 Azure CLI 部署 Azure API for FHIR

在本快速入门中，你将了解如何使用 Azure CLI 在 Azure 中部署 Azure API for FHIR。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>添加 HealthcareAPIs 扩展

```azurecli-interactive
az extension add --name healthcareapis
```

获取 HealthcareAPIs 的命令列表：

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>创建 Azure 资源组

为将包含 Azure API for FHIR 的资源组选择一个名称并创建它：

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>部署 Azure API for FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>提取 FHIR API 功能语句

使用以下 CLI 从 FHIR API 获取功能语句：

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除资源组：

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门指南中，你已将 Azure API for FHIR 部署到订阅中。 若要在 Azure API for FHIR 中设置其他设置，请转到其他设置操作指南。

>[!div class="nextstepaction"]
>[Azure API for FHIR 中的其他设置](azure-api-for-fhir-additional-settings.md)
