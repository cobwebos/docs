---
title: 管理机器学习工作区 | Microsoft Docs
description: 管理对 Azure 机器学习工作区的访问，部署并管理 ML API Web 服务
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c8e65d5fd7efd1f9212b76e1a72b91a0ea07272
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835060"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>管理 Azure 机器学习工作区

> [!NOTE]
> 有关在机器学习 Web 服务门户中管理 Web 服务的信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。
> 
> 

可以在 Azure 门户中管理机器学习工作区。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>使用 Azure 门户

若要在 Azure 门户中管理工作区，请执行以下操作：

1. 使用 Azure 订阅管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 在页面顶部的搜索框中，输入“机器学习工作区”，并选择“机器学习工作区”。
3. 单击想要管理的工作区。

除了标准的资源管理信息和可用选项外，还可以：

- 查看**属性** - 此页显示工作区和资源信息，并可以更改此工作区连接到的订阅和资源组。
- **重新同步存储密钥** - 此工作区维护存储帐户的密钥。 如果存储帐户更改密钥，则可以单击“重新同步密钥”将密钥与工作区同步。

若要管理与此工作区关联的 Web 服务，请使用“机器学习 Web 服务”门户。 有关完整信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新 Web 服务，必须分配有该 Web 服务部署到的订阅上的参与者或管理员角色。 如果邀请其他用户加入机器学习工作区，必须向其分配订阅上的参与者或管理员角色，他们才能部署或管理 Web 服务。 
> 
>有关设置访问权限的详细信息，请参阅[在 Azure 门户中查看用户和组的访问权限分配](../../role-based-access-control/role-assignments-users.md)。

## <a name="next-steps"></a>后续步骤
* 详细了解[使用 Azure 资源管理器模板部署机器学习](deploy-with-resource-manager-template.md)。 