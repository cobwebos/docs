---
title: 使用 Azure CLI 创建 IoT 中心 | Microsoft Docs
description: 了解如何使用 Azure CLI 命令创建资源组，然后在资源组中创建 IoT 中心。 还了解如何删除中心。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 69372e4c212e2ce81bcd4c91d460aa191a1d3476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087841"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍如何使用 Azure CLI 创建 IoT 中心。

## <a name="prerequisites"></a>必备条件

若要完成本操作说明，需要 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

如果在本地运行 Azure CLI 而不是使用 Cloud Shell，则需要登录 Azure 帐户。

在命令提示符中，运行 [login 命令](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)：

   ```azurecli
   az login
   ```

按照说明使用代码进行身份验证，并通过 Web 浏览器登录 Azure 帐户。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建资源组，并添加 IoT 中心。

1. 创建 IoT 中心时，必须在资源组中创建它。 使用现有资源组，或运行以下[命令创建资源组](https://docs.microsoft.com/cli/azure/resource)：
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 上一示例在美国西部位置创建资源组。 可以通过运行以下命令来查看可用位置列表： 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. 运行以下[命令，使用 IoT 中心的全局唯一名称在资源组中创建 IoT 中心](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create)：
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上一命令在计费的 S1 定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="remove-an-iot-hub"></a>删除 IoT 中心

可使用 Azure CLI [删除单个资源](https://docs.microsoft.com/cli/azure/resource)（例如 IoT 中心），或删除资源组及其所有资源（包括任何 IoT 中心）。

若要 [删除 IoT 中心](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)，请运行以下命令：

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

若要 [删除资源组](https://docs.microsoft.com/cli/azure/group#az-group-delete) 及其所有资源，请运行以下命令：

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 IoT 中心，请参阅以下文章：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure 门户管理 IoT 中心](iot-hub-create-through-portal.md)
