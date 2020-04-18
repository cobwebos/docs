---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616819"
---
> [!TIP]
> 登录后，你将看到与你的 Azure 帐户关联的订阅列表。 使用的`isDefault: true`订阅信息是当前激活的 Azure CLI 命令的订阅。 此订阅必须与包含 Azure 机器学习工作区的订阅相同。 您可以通过访问工作区的概述页从[Azure 门户](https://portal.azure.com)找到订阅 ID。 您还可以使用 SDK 从工作区对象获取订阅 ID。 例如，`Workspace.from_config().subscription_id` 。
> 
> 要选择其他订阅，请使用`az account set -s <subscription name or ID>`命令并指定要切换到的订阅名称或 ID。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。