---
title: Azure Data Share 的角色和要求
description: 了解使用 Azure 数据共享来共享和接收数据所需的权限。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087171"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和要求 

本文介绍使用 Azure 数据共享服务共享和接收数据所需的角色和权限。 

## <a name="roles-and-requirements"></a>角色和要求

使用 Azure 数据共享服务，无需在数据访问接口和使用者之间交换凭据即可共享数据。 Azure 数据共享服务使用托管标识（以前称为 Msi）在 Azure 数据存储中进行身份验证。 

需要为 azure 数据共享资源的托管标识授予对 Azure 数据存储的访问权限。 然后，Azure 数据共享服务使用此托管标识来读取和写入数据，以进行基于快照的共享，并建立用于就地共享的符号链接。 

若要在 Azure 数据存储中共享或接收数据，用户需要至少具有以下权限。 对于基于 SQL 的共享，需要其他权限。
* 写入 Azure 数据存储的权限。 通常，此权限存在于**参与者**角色中。
* 在 Azure 数据存储中创建角色分配的权限。 通常，创建角色分配的权限存在于**所有者**角色、用户访问管理员角色或分配了 Microsoft 的自定义角色中。 如果数据共享资源的托管标识已被授予对 Azure 数据存储的访问权限，则不需要此权限。 请参阅下表了解必需的角色。

下面是分配给数据共享资源的托管标识的角色的摘要：

| |  |  |
|---|---|---|
|**数据存储类型**|**数据访问接口源数据存储**|**数据使用者目标数据存储**|
|Azure Blob 存储| 存储 Blob 数据读取器 | 存储 Blob 数据参与者
|Azure Data Lake Gen1 | 所有者 | 不支持
|Azure Data Lake Gen2 | 存储 Blob 数据读取器 | 存储 Blob 数据参与者
|Azure SQL Server | SQL DB 参与者 | SQL DB 参与者
|Azure 数据资源管理器群集 | Contributor | Contributor
|

对于基于 SQL 的共享，需要在 SQL 数据库中创建一个与 Azure 数据共享资源同名的外部提供程序的 SQL 用户。 下面汇总了 SQL 用户所需的权限。

| |  |  |
|---|---|---|
|**SQL 数据库类型**|**数据访问接口 SQL 用户权限**|**数据使用者 SQL 用户权限**|
|Azure SQL 数据库 | db_datareader | db_datareader、db_datawriter db_ddladmin
|Azure Synapse Analytics（以前称为 SQL DW） | db_datareader | db_datareader、db_datawriter db_ddladmin
|


### <a name="data-provider"></a>数据访问接口 
若要在 Azure 数据共享中添加数据集，需要授予访问接口数据共享资源的托管标识访问源 Azure 数据存储的权限。 例如，在存储帐户的情况下，会向数据共享资源的托管标识授予存储 Blob 数据读取者角色。 

当用户通过 Azure 门户添加数据集，并且用户具有适当的权限时，Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是分配了 Microsoft. Authorization/role 分配/写入权限的自定义角色的成员。 

或者，用户可以将数据共享资源的托管标识手动添加到 Azure 数据存储中。 每个数据共享资源只需要执行一次此操作。

若要为数据共享资源的托管标识创建角色分配，请执行以下步骤：

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择 "**添加角色分配**"。
1. 在 "*角色*" 下，选择上一个角色分配表中的角色（例如，对于 "存储帐户"，选择 "*存储 Blob 数据读取器*"）。
1. 在 "*选择*" 下，键入 Azure 数据共享资源的名称。
1. 单击 *“保存”* 。

对于基于 SQL 的源，除了以上步骤以外，还需要从 SQL 数据库中的外部提供程序创建 SQL 用户，其名称与 Azure 数据共享资源的名称相同。 需要授予此用户*db_datareader*权限。 可以在 "[共享数据](share-your-data.md)" 教程中找到与基于 SQL 的共享一起使用的示例脚本和其他先决条件。 

### <a name="data-consumer"></a>数据使用者
若要接收数据，需要向使用者数据共享资源的托管标识授予对目标 Azure 数据存储的访问权限。 例如，在存储帐户的情况下，会向数据共享资源的托管标识授予存储 Blob 数据参与者角色。 

如果用户通过 Azure 门户指定目标数据存储，并且用户具有适当的权限，则 Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是分配了 Microsoft. Authorization/role 分配/写入权限的自定义角色的成员。 

或者，用户可以将数据共享资源的托管标识手动添加到 Azure 数据存储中。 每个数据共享资源只需要执行一次此操作。

若要手动创建数据共享资源的托管标识的角色分配，请执行以下步骤：

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择 "**添加角色分配**"。
1. 在 "*角色*" 下，选择上一个角色分配表中的角色（例如，对于 "存储帐户"，选择 "*存储 Blob 数据读取器*"）。
1. 在 "*选择*" 下，键入 Azure 数据共享资源的名称。
1. 单击 *“保存”* 。

对于基于 SQL 的目标，除了以上步骤以外，还需要从 SQL 数据库中的外部提供程序创建 SQL 用户，其名称与 Azure 数据共享资源的名称相同。 此用户需要被授予*db_datareader、db_datawriter db_ddladmin*权限。 可以在[接受和接收数据](subscribe-to-data-share.md)教程中找到一个示例脚本以及基于 SQL 的共享的其他必备组件。 

如果使用 REST Api 共享数据，则需要手动创建这些角色分配。 

若要了解有关如何添加角色分配的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。 

## <a name="resource-provider-registration"></a>资源提供程序注册 

若要在 Azure 租户中首次查看 Azure 数据共享邀请，你可能需要手动将 DataShare 资源提供程序注册到你的 Azure 订阅中。 按照以下步骤将 DataShare 资源提供程序注册到你的 Azure 订阅。 需要对 Azure 订阅的*参与者*访问权限才能注册资源提供程序。

1. 在 Azure 门户中，导航到 "**订阅**"。
1. 选择要用于 Azure 数据共享的订阅。
1. 单击 "**资源提供程序**"。
1. 搜索 DataShare。
1. 单击“注册”。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)

