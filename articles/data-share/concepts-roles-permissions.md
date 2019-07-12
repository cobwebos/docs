---
title: 角色和 Azure 数据共享 Preview 的要求
description: 角色和 Azure 数据共享 Preview 的要求
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807525"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>角色和 Azure 数据共享 Preview 的要求

本指南介绍了共享数据使用 Azure 数据共享预览，也接受和接收数据使用 Azure 数据共享预览版所需的角色。 

## <a name="roles-and-requirements"></a>角色和要求

若要共享或接收数据使用 Azure 数据共享，用来登录到 Azure 的用户帐户必须能够授予数据共享到您的数据共享或接收到数据中的存储帐户的权限。 通常，这是中存在的权限**所有者**角色或与分配的 Microsoft.Authorization/role 分配/写权限的自定义角色。 

若要共享或从 / 向 Azure 存储帐户接收的数据，必须是存储帐户的所有者。 即使你已创建存储帐户，这不会自动授予您的存储帐户的所有权。 若要将您在添加到你的 Azure 存储帐户的所有者角色，请执行以下步骤。

1. 导航到在 Azure 门户中的存储帐户
1. 选择**访问控制 (IAM)**
1. 单击“添加” 
1. 将在您自己添加为所有者

若要查看你在订阅中拥有的权限，请在 Azure 门户中，选择右上角的用户名，然后选择“权限”。  如果可以访问多个订阅，请选择相应的订阅。 

## <a name="resource-provider-registration"></a>资源提供程序注册 

时接受的 Azure 数据共享邀请，你将需要手动注册到你的订阅中的 Microsoft.DataShare 资源提供程序。 请按照下列步骤以将 Microsoft.DataShare 资源提供程序注册到你的 Azure 订阅。 

1. 在 Azure 门户中，导航到**订阅**
1. 选择使用适用于 Azure 的数据共享的订阅
1. 单击**资源提供程序**
1. 搜索 Microsoft.DataShare
1. 单击“注册” 

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)

