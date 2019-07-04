---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172726"
---
## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源（例如函数应用、数据库和存储帐户）的逻辑容器。

以下示例创建名为 `myResourceGroup` 的资源组。  
如果不使用云 Shell，请先使用 `az login` 登录。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
通常在附近的区域中创建资源组和资源。 若要查看应用服务计划的所有支持位置，请运行 [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations) 命令。
