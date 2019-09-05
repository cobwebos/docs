---
title: Azure 数据共享预览版的角色和要求
description: Azure 数据共享预览版的角色和要求
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7bf98f8774551292574d4f1951eba44657fa7de0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307359"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Azure 数据共享预览版的角色和要求

本文介绍了使用 Azure 数据共享预览来共享数据所需的角色，以及如何使用 Azure 数据共享预览来接受和接收数据。 

## <a name="roles-and-requirements"></a>角色和要求

Azure 数据共享使用 Azure 服务的托管标识（以前称为 Msi）对基础存储帐户进行身份验证，以便能够读取数据提供程序共享的数据，以及接收作为数据使用者共享的数据。 因此，数据提供程序和数据使用者之间不会交换凭据。 

需要授予托管服务标识对基础存储帐户的访问权限。 Azure 数据共享服务使用 Azure 数据共享资源的托管服务标识来读取和写入数据。 Azure 数据共享的用户需要能够为要从/向其共享数据的存储帐户创建托管服务标识角色分配。 **所有者**角色、用户访问管理员角色或分配了 Microsoft 的自定义角色中存在创建角色分配的权限。 

如果你不是相关存储帐户的所有者，并且无法自行为 Azure 数据共享资源的托管标识创建角色分配，则可以请求 Azure 管理员以你的名义创建角色分配。 

下面是分配给数据共享资源托管标识的角色的摘要：

| |  |  |
|---|---|---|
|**存储类型**|**数据访问接口源存储帐户**|**数据使用者目标存储帐户**|
|Azure Blob 存储| 存储 Blob 数据读取器 | 存储 Blob 数据参与者
|Azure Data Lake Gen1 | 所有者 | 不受支持
|Azure Data Lake Gen2 | 存储 Blob 数据读取器 | 存储 Blob 数据参与者
|
### <a name="data-providers"></a>数据访问接口 
若要将数据集添加到 Azure 数据共享，需要将数据访问接口数据共享资源托管标识添加到存储 Blob 数据读取器角色。 如果用户是通过 Azure 添加数据集，并且是存储帐户的所有者，或者是分配了 Microsoft 授权/角色分配/写入权限的自定义角色的成员，则 Azure 数据共享服务会自动完成此操作。 

或者，用户可以让 Azure 管理员手动将数据共享资源管理标识添加到存储 Blob 数据读取器角色。 如果管理员手动创建此角色分配，则必须是存储帐户的所有者或具有自定义角色分配。 这适用于从 Azure 存储或 Azure Data Lake Gen2 共享的数据。 

如果从 Azure Data Lake Gen1 共享数据，则必须将角色分配给所有者角色。 

若要为数据共享资源的托管标识创建角色分配，请执行以下步骤：

1. 导航到存储帐户。
1. 选择“访问控制 (IAM)”。
1. 选择 "**添加角色分配**"。
1. 在 "*角色*" 下，选择 "*存储 Blob 数据读取器*"。
1. 在 "*选择*" 下，键入 Azure 数据共享帐户的名称。
1. 单击“保存”。

### <a name="data-consumers"></a>数据使用者
若要接收数据，需要将数据使用者数据共享资源托管标识添加到存储 Blob 数据参与者角色。 要使 Azure 数据共享服务能够写入存储帐户，此角色是必需的。 如果用户是通过 Azure 添加数据集，并且是存储帐户的所有者，或者是分配了 Microsoft. Authorization/role 分配/写入权限的自定义角色的成员，则 Azure 数据共享服务会自动完成此操作。 

或者，用户可以让 Azure 管理员手动将数据共享资源管理标识添加到存储 Blob 数据参与者角色。 如果管理员手动创建此角色分配，则必须是存储帐户的所有者或具有自定义角色分配。 请注意，这适用于将数据共享到 Azure 存储或 Azure Data Lake Gen2。 不支持将数据接收到 Azure Data Lake Gen1。 

若要手动创建数据共享资源的托管标识的角色分配，请执行以下步骤：

1. 导航到存储帐户。
1. 选择“访问控制 (IAM)”。
1. 选择 "**添加角色分配**"。
1. 在 "*角色*" 下，选择 "*存储 Blob 数据参与者*"。 
1. 在 "*选择*" 下，键入 Azure 数据共享帐户的名称。
1. 单击“保存”。

如果要使用 REST Api 共享数据，则需要通过将中的数据共享帐户添加到相应的角色中，手动创建这些角色分配。 

若要了解有关如何添加角色分配的详细信息，请参阅[此文档，](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)其中概述了如何将角色分配添加到 Azure 资源。 

## <a name="resource-provider-registration"></a>资源提供程序注册 

接受 Azure 数据共享邀请时，需要手动将 DataShare 资源提供程序注册到订阅。 按照以下步骤将 DataShare 资源提供程序注册到你的 Azure 订阅。 

1. 在 Azure 门户中，导航到 "**订阅**"。
1. 选择要用于 Azure 数据共享的订阅。
1. 单击 "**资源提供程序**"。
1. 搜索 DataShare。
1. 单击“注册”。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)

