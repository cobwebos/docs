---
title: Azure CLI：部署适用于 Azure 的开放源代码 FHIR 服务器 - Azure API for Azure
description: 本快速入门介绍了如何部署适用于 Azure 的开放源代码 Microsoft FHIR 服务器。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819971"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>快速入门：使用 Azure CLI 部署开放源代码 FHIR 服务器

本快速入门介绍如何使用 Azure CLI 在 Azure 中部署开放源代码 FHIR&reg; 服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>创建资源组

为将包含预配资源的资源组选择一个名称并创建它：

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>部署模板

适用于 Azure 的 Microsoft FHIR 服务器 [GitHub 存储库](https://github.com/Microsoft/fhir-server)包含一个将部署所有必需资源的模板。 使用以下命令部署它：

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>验证 FHIR 服务器是否正在运行

使用以下命令从 FHIR 服务器获取功能语句：

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

服务器首次响应需要 1 分钟或更长时间。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除资源组：

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已将适用于 Azure 的 Microsoft 开源 FHIR 服务器部署到订阅中。 若要了解如何使用 Postman 访问 FHIR API，请转到 Postman 教程。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR API](access-fhir-postman-tutorial.md)