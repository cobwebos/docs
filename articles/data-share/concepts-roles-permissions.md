---
title: Azure Data Share 的角色和要求
description: 了解使用 Azure 数据共享共享和接收数据所需的权限。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265501"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和要求 

本文介绍了使用 Azure 数据共享服务共享和接收数据所需的角色和权限。 

## <a name="roles-and-requirements"></a>角色和要求

使用 Azure 数据共享服务，无需在数据提供程序和使用者之间交换凭据即可共享数据。 Azure 数据共享服务使用托管标识（以前称为 MSI）对 Azure 数据存储进行身份验证。 

需要授予 Azure 数据共享资源的托管标识对 Azure 数据存储的访问权限。 然后，Azure 数据共享服务使用此托管标识读取和写入数据以进行基于快照的共享，并为就地共享建立符号链接。 

要共享或接收来自 Azure 数据存储的数据，用户至少需要以下权限。 基于 SQL 的共享需要额外的权限。
* 写入 Azure 数据存储的权限。 通常，此权限存在于 **"参与者"** 角色中。
* 在 Azure 数据存储中创建角色分配的权限。 通常，创建角色分配的权限存在于 Microsoft**的"所有者"** 角色、用户访问管理员角色或自定义角色中。 如果数据共享资源的托管标识已被授予对 Azure 数据存储的访问权限，则不需要此权限。 有关所需角色，请参阅下表。

以下是分配给数据共享资源的托管标识的角色摘要：

| |  |  |
|---|---|---|
|**数据存储类型**|**数据提供程序源数据存储**|**数据使用者目标数据存储**|
|Azure Blob 存储| 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|Azure Data Lake Gen1 | “所有者” | 不支持
|Azure Data Lake Gen2 | 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|Azure SQL Server | SQL DB 参与者 | SQL DB 参与者
|Azure 数据资源管理器群集 | 参与者 | 参与者
|

对于基于 SQL 的共享，需要从 SQL 数据库中与 Azure 数据共享资源同名的外部提供程序创建 SQL 用户。 下面是 SQL 用户所需的权限摘要。

| |  |  |
|---|---|---|
|**SQL 数据库类型**|**数据提供程序 SQL 用户权限**|**数据使用者 SQL 用户权限**|
|Azure SQL 数据库 | db_datareader | db_datareader，db_datawriter，db_ddladmin
|Azure Synapse Analytics（以前称为 SQL DW） | db_datareader | db_datareader，db_datawriter，db_ddladmin
|


### <a name="data-provider"></a>数据提供程序 
要在 Azure 数据共享中添加数据集，需要授予提供程序数据共享资源的托管标识对源 Azure 数据存储的访问权限。 例如，在存储帐户中，数据共享资源的托管标识被授予存储 Blob 数据读取器角色。 

当用户通过 Azure 门户添加数据集且用户具有适当的权限时，Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是具有 Microsoft 的自定义角色的成员。 

或者，用户可以让 Azure 数据存储的所有者手动将数据共享资源的托管标识添加到 Azure 数据存储中。 此操作只需执行每个数据共享资源一次。

要为数据共享资源的托管标识创建角色分配，请按照以下步骤操作：

1. 导航到 Azure 数据存储。
1. 选择**访问控制 （IAM）。**
1. 选择 **"添加角色分配**"。
1. 在 *"角色*"下，选择上面的角色分配表中的角色（例如，对于存储帐户，选择*存储 Blob 数据读取器*）。
1. 在 *"选择*"下，键入 Azure 数据共享资源的名称。
1. 单击“保存”。**

对于基于 SQL 的源，除了上述步骤外，还需要从 SQL 数据库中与 Azure 数据共享资源同名的外部提供程序创建 SQL 用户。 需要授予此用户*db_datareader*权限。 在[共享数据](share-your-data.md)教程中可以找到示例脚本以及基于 SQL 的共享的其他先决条件。 

### <a name="data-consumer"></a>数据使用者
要接收数据，需要授予使用者数据共享资源的托管标识对目标 Azure 数据存储的访问权限。 例如，在存储帐户中，数据共享资源的托管标识被授予存储 Blob 数据参与者角色。 

如果用户通过 Azure 门户指定目标数据存储且用户具有适当的权限，则 Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是具有 Microsoft 的自定义角色的成员。 

或者，用户可以让 Azure 数据存储的所有者手动将数据共享资源的托管标识添加到 Azure 数据存储中。 此操作只需执行每个数据共享资源一次。

要手动为数据共享资源的托管标识创建角色分配，请按照以下步骤操作：

1. 导航到 Azure 数据存储。
1. 选择**访问控制 （IAM）。**
1. 选择 **"添加角色分配**"。
1. 在 *"角色*"下，选择上面的角色分配表中的角色（例如，对于存储帐户，选择*存储 Blob 数据读取器*）。
1. 在 *"选择*"下，键入 Azure 数据共享资源的名称。
1. 单击“保存”。**

对于基于 SQL 的目标，除了上述步骤外，还需要从 SQL 数据库中与 Azure 数据共享资源同名的外部提供程序创建 SQL 用户。 需要授予此用户*db_datareader、db_datawriter、db_ddladmin*权限。 示例脚本以及基于 SQL 的共享的其他先决条件可以在[接受和接收数据](subscribe-to-data-share.md)教程中找到。 

如果使用 REST API 共享数据，则需要手动创建这些角色分配。 

要了解有关如何添加角色分配的详细信息，请参阅[本文档](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。 

## <a name="resource-provider-registration"></a>资源提供商注册 

要首次在 Azure 租户中查看 Azure 数据共享邀请，可能需要手动将 Microsoft.DataShare 资源提供程序注册到 Azure 订阅中。 按照以下步骤将 Microsoft.DataShare 资源提供程序注册到 Azure 订阅中。 您需要对 Azure 订阅*的参与者*访问权限来注册资源提供程序。

1. 在 Azure 门户中，导航到**订阅**。
1. 选择用于 Azure 数据共享的订阅。
1. 单击**资源提供程序**。
1. 搜索微软.数据共享。
1. 单击“注册”****。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)

