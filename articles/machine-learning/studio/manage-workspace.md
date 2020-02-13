---
title: 管理工作区
titleSuffix: ML Studio (classic) - Azure
description: 管理对 Azure 机器学习 Studio （经典）工作区的访问，并部署和管理机器学习 API web 服务
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 996cf4451811b46a99e7c0b64ca0fa21c1eba5f9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168483"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>管理 Azure 机器学习 Studio （经典）工作区

> [!NOTE]
> 有关在机器学习 Web 服务门户中管理 Web 服务的信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。
> 
> 

可以在 Azure 门户中管理机器学习 Studio （经典）工作区。



## <a name="use-the-azure-portal"></a>使用 Azure 门户

若要在 Azure 门户中管理 Studio （经典）工作区：

1. 使用 Azure 订阅管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 在页面顶部的 "搜索" 框中，输入 "机器学习工作室（经典）工作区"，然后选择 "**机器学习 Studio （经典）工作区**"。
3. 单击想要管理的工作区。

除了标准的资源管理信息和可用选项外，还可以：

- 查看**属性** - 此页显示工作区和资源信息，并可以更改此工作区连接到的订阅和资源组。
- **重新同步存储密钥** - 此工作区维护存储帐户的密钥。 如果存储帐户更改密钥，则可以单击“重新同步密钥”将密钥与工作区同步。

若要管理与此 Studio （经典）工作区关联的 web 服务，请使用机器学习 Web 服务门户。 有关完整信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新 Web 服务，必须分配有该 Web 服务部署到的订阅上的参与者或管理员角色。 如果将其他用户邀请到机器学习工作室（经典）工作区，则必须先将其分配给订阅上的参与者或管理员角色，然后才能部署或管理 web 服务。 
> 
>有关设置访问权限的更多信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤
* 详细了解[使用 Azure 资源管理器模板部署机器学习](deploy-with-resource-manager-template.md)。 
