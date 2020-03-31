---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296886"
---
登录后，你将看到与你的 Azure 帐户关联的订阅列表。 使用的`isDefault: true`订阅信息是当前激活的 Azure CLI 命令的订阅。 此订阅必须与包含 Azure 机器学习工作区的订阅相同。 您可以通过访问工作区的概述页从[Azure 门户](https://portal.azure.com)找到订阅 ID。 您还可以使用 SDK 从工作区对象获取订阅 ID。 例如，`Workspace.from_config().subscription_id` 。
    
若要选择另一个订阅，请将 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 命令与要切换到的订阅 ID 配合使用。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。