---
title: Azure Blockchain Workbench database views
description: Overview of available Azure Blockchain Workbench Preview SQL DB database views.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325985"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure Blockchain Workbench database views

Azure Blockchain Workbench Preview delivers data from distributed ledgers to an *off-chain* SQL DB database. The off-chain database makes it possible to use SQL and existing tools, such as [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), to interact with blockchain data.

Azure Blockchain Workbench 提供一组数据库视图用于访问有助于执行查询的数据。 这些视图经过很大力度的非规范化，因此，可以快速轻松地使用它们开始生成报告、分析，或者结合现有工具使用区块链数据，而无需重新培训数据库工作人员。

本部分概述数据库视图及其包含的数据。

> [!NOTE]
> 不支持在这些视图的外部直接使用数据库中的数据库表（如果存在这种情况）。
>

## <a name="vwapplication"></a>vwApplication

此视图提供有关已上传到 Azure Blockchain Workbench 的**应用程序**的详细信息。

| 名称                             | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                  | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDescription           | nvarchar(255) | 是         | 应用程序的说明 |
| ApplicationDisplayName           | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled               | bit           | No          | 标识当前是否已启用应用程序<br /> **注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。 |
| UploadedDtTm                     | datetime2(7)  | No          | 上传合同的日期和时间 |
| UploadedByUserId                 | int           | No          | 上传应用程序的用户的 ID |
| UploadedByUserExternalId         | nvarchar(255) | No          | 上传应用程序的用户的外部标识符。 By default, this ID is the user from the Azure Active Directory for the consortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | No          | 标识用户的当前预配过程状态。 可能的值包括： <br />0 – API 已创建用户<br />1 – 已将密钥与数据库中的用户相关联<br />2 – 已完全预配用户                         |
| UploadedByUserFirstName          | nvarchar(50)  | 是         | 上传合同的用户的名字 |
| UploadedByUserLastName           | nvarchar(50)  | 是         | 上传合同的用户的姓氏 |
| UploadedByUserEmailAddress       | nvarchar(255) | 是         | 上传合同的用户的电子邮件地址 |

## <a name="vwapplicationrole"></a>vwApplicationRole

此视图提供有关 Azure Blockchain Workbench 应用程序中定义的角色的详细信息。

例如，在“资产转让”应用程序中，可以定义“买方”和“卖方”等角色。

| 名称                   | Type             | 可为 Null | 描述                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | No          | 应用程序的唯一标识符           |
| ApplicationName        | nvarchar(50)     | No          | 应用程序的名称                       |
| ApplicationDescription | nvarchar(255)    | 是         | 应用程序的说明                  |
| ApplicationDisplayName | nvarchar(255)    | No          | 在用户界面中显示的名称      |
| RoleId                 | int              | No          | 应用程序中角色的唯一标识符 |
| RoleName               | nvarchar50)      | No          | 角色的名称                              |
| RoleDescription        | description(255) | 是         | 角色的说明                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

此视图提供有关 Azure Blockchain Workbench 应用程序中定义的角色及其关联的用户的详细信息。

例如，在“资产转让”应用程序中，*John Smith* 可能与“买方”角色相关联。

| 名称                       | Type          | 可为 Null | 描述                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | No          | 应用程序的唯一标识符                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | No          | 应用程序的名称                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | 是         | 应用程序的说明                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | No          | 在用户界面中显示的名称                                                                                                                                                                                          |
| ApplicationRoleId          | int           | No          | 应用程序中角色的唯一标识符                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | No          | 角色的名称                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | 是         | 角色的说明                                                                                                                                                                                                             |
| UserId                     | int           | No          | 与角色关联的用户的 ID |
| UserExternalId             | nvarchar(255) | No          | 与角色关联的用户的外部标识符。 By default, this ID is the user from the Azure Active Directory for the consortium.                                                                     |
| UserProvisioningStatus     | int           | No          | 标识用户的当前预配过程状态。 可能的值包括： <br />0 – API 已创建用户<br />1 – 已将密钥与数据库中的用户相关联<br />2 – 已完全预配用户 |
| UserFirstName              | nvarchar(50)  | 是         | 与角色关联的用户的名字 |
| UserLastName               | nvarchar(255) | 是         | 与角色关联的用户的姓氏 |
| UserEmailAddress           | nvarchar(255) | 是         | 与角色关联的用户的电子邮件地址 |

## <a name="vwconnectionuser"></a>vwConnectionUser

此视图提供有关 Azure Blockchain Workbench 中定义的连接及其关联的用户的详细信息。 对于每个连接，此视图包含以下数据：

-   关联的账本详细信息
-   关联的用户信息

| 名称                     | Type          | 可为 Null | 描述                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | No          | Azure Blockchain Workbench 中的连接的唯一标识符 |
| ConnectionEndpointUrl    | nvarchar(50)  | No          | 连接的终结点 URL |
| ConnectionFundingAccount | nvarchar(255) | 是         | 与连接关联的投资帐户（如果适用） |
| LedgerId                 | int           | No          | 账本的唯一标识符 |
| LedgerName               | nvarchar(50)  | No          | 账本的名称 |
| LedgerDisplayName        | nvarchar(255) | No          | 在 UI 中显示的账本名称 |
| UserId                   | int           | No          | 与连接关联的用户的 ID |
| UserExternalId           | nvarchar(255) | No          | 与连接关联的用户的外部标识符。 By default, this ID is the user from the Azure Active Directory for the consortium. |
| UserProvisioningStatus   | int           | No          |标识用户的当前预配过程状态。 可能的值包括： <br />0 – API 已创建用户<br />1 – 已将密钥与数据库中的用户相关联<br />2 – 已完全预配用户 |
| UserFirstName            | nvarchar(50)  | 是         | 与连接关联的用户的名字 |
| UserLastName             | nvarchar(255) | 是         | 与连接关联的用户的姓氏 |
| UserEmailAddress         | nvarchar(255) | 是         | 与连接关联的用户的电子邮件地址 |

## <a name="vwcontract"></a>vwContract

此视图提供有关已部署的合同的详细信息。 对于每个合同，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   关联的函数账本实现
-   启动操作的用户的详细信息
-   区块链块和事务相关的详细信息

| 名称                                     | Type           | 可为 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | No          | Azure Blockchain Workbench 中的连接的唯一标识符。                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | No          | 连接的终结点 URL |
| ConnectionFundingAccount                 | nvarchar(255)  | 是         | 与连接关联的投资帐户（如果适用） |
| LedgerId                                 | int            | No          | 账本的唯一标识符 |
| LedgerName                               | nvarchar(50)   | No          | 账本的名称 |
| LedgerDisplayName                        | nvarchar(255)  | No          | 在 UI 中显示的账本名称 |
| ApplicationId                            | int            | No          | 应用程序的唯一标识符 |
| ApplicationName                          | nvarchar (50)  | No          | 应用程序的名称 |
| ApplicationDisplayName                   | nvarchar (255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                       | bit            | No          | 标识当前是否已启用应用程序。<br /> **注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。  |
| WorkflowId                               | int            | No          | 与合同关联的工作流的唯一标识符 |
| WorkflowName                             | nvarchar(50)   | No          | 与合同关联的工作流的名称 |
| WorkflowDisplayName                      | nvarchar(255)  | No          | 在用户界面中显示的、与合同关联的工作流的名称 |
| WorkflowDescription                      | nvarchar(255)  | 是         | 与合同关联的工作流的说明 |
| ContractCodeId                           | int            | No          | 与合同关联的合同代码的唯一标识符 |
| ContractFileName                         | int            | No          | 包含此工作流的智能合同代码的文件的名称。 |
| ContractUploadedDtTm                     | int            | No          | 上传合同代码的日期和时间 |
| ContractId                               | int            | No          | 合同的唯一标识符 |
| ContractProvisioningStatus               | int            | No          | 标识合同的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建合同<br />1 – 合同已发送到账本<br />2 – 合同已成功部署到账本<br />3 或 4 - 合同无法部署到账本<br />5 – 合同已成功部署到账本 <br /><br />从版本 1.5 开始，支持值 0 到 5。 为了在当前版本中向后兼容，提供了仅支持值 0 到 2 的视图 **vwContractV0**。 |
| ContractLedgerIdentifier                 | nvarchar (255) |             | 部署合同的用户的电子邮件地址 |
| ContractDeployedByUserId                 | int            | No          | 部署合同的用户的外部标识符。 By default, this ID is the guid representing the Azure Active Directory ID for the user.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | No          | 部署合同的用户的外部标识符。 By default, this ID is the guid representing the Azure Active Directory ID for the user.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | No          | 标识用户的当前预配过程状态。 可能的值包括： <br />0 – API 已创建用户<br />1 – 已将密钥与数据库中的用户相关联 <br />2 – 已完全预配用户                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | 是         | 部署合同的用户的名字 |
| ContractDeployedByUserLastName           | nvarchar(255)  | 是         | 部署合同的用户的姓氏 |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | 是         | 部署合同的用户的电子邮件地址 |

## <a name="vwcontractaction"></a>vwContractAction

此视图呈现针对合同执行的操作相关的大部分信息，方便促成常见的报告方案。 对于执行的每项操作，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   关联的智能合同函数和参数定义
-   关联的函数账本实现
-   为参数提供的特定实例值
-   启动操作的用户的详细信息
-   区块链块和事务相关的详细信息

| 名称                                     | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                          | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName                   | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                       | bit           | No          | 此字段标识当前是否已启用应用程序。 注意 – 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。                                                  |
| WorkflowId                               | int           | No          | 工作流的唯一标识符 |
| WorkflowName                             | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName                      | nvarchar(255) | No          | 在用户界面中显示的工作流名称 |
| WorkflowDescription                      | nvarchar(255) | 是         | 工作流的说明 |
| ContractId                               | int           | No          | 合同的唯一标识符 |
| ContractProvisioningStatus               | int           | No          | 标识合同的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建合同<br />1 – 合同已发送到账本<br />2 – 合同已成功部署到账本<br />3 或 4 - 合同无法部署到账本<br />5 – 合同已成功部署到账本 <br /><br />从版本 1.5 开始，支持值 0 到 5。 为了在当前版本中向后兼容，提供了仅支持值 0 到 2 的视图 **vwContractActionV0**。 |
| ContractCodeId                           | int           | No          | 合同代码实现的唯一标识符 |
| ContractLedgerIdentifier                 | nvarchar(255) | 是         | 与特定分布式账本的已部署智能合同版本关联的唯一标识符。 例如 Ethereum。 |
| ContractDeployedByUserId                 | int           | No          | 部署合同的用户的唯一标识符 |
| ContractDeployedByUserFirstName          | nvarchar(50)  | 是         | 部署合同的用户的名字 |
| ContractDeployedByUserLastName           | nvarchar(255) | 是         | 部署合同的用户的姓氏 |
| ContractDeployedByUserExternalId         | nvarchar(255) | No          | 部署合同的用户的外部标识符。 By default, this ID is the guid that represents their identity in the consortium Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | 是         | 部署合同的用户的电子邮件地址 |
| WorkflowFunctionId                       | int           | No          | 工作流函数的唯一标识符 |
| WorkflowFunctionName                     | nvarchar(50)  | No          | 函数的名称 |
| WorkflowFunctionDisplayName              | nvarchar(255) | No          | 在用户界面中显示的函数名称 |
| WorkflowFunctionDescription              | nvarchar(255) | No          | 函数的说明 |
| ContractActionId                         | int           | No          | 合同操作的唯一标识符 |
| ContractActionProvisioningStatus         | int           | No          | 标识合同操作的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建合同操作<br />1 – 合同操作已发送到账本<br />2 – 合同操作已成功部署到账本<br />3 或 4 - 合同无法部署到账本<br />5 – 合同已成功部署到账本 <br /><br />从版本 1.5 开始，支持值 0 到 5。 为了在当前版本中向后兼容，提供了仅支持值 0 到 2 的视图 **vwContractActionV0**。 |
| ContractActionTimestamp                  | datetime(2,7) | No          | 合同操作的时间戳 |
| ContractActionExecutedByUserId           | int           | No          | 执行合同操作的用户的唯一标识符 |
| ContractActionExecutedByUserFirstName    | int           | 是         | 执行合同操作的用户的名字 |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | 是         | 执行合同操作的用户的姓氏 |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | 是         | 执行合同操作的用户的外部标识符。 By default, this ID is the guid that represents their identity in the consortium Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | 是         | 执行合同操作的用户的电子邮件地址 |
| WorkflowFunctionParameterId              | int           | No          | 函数的参数的唯一标识符 |
| WorkflowFunctionParameterName            | nvarchar(50)  | No          | 函数的参数的名称 |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | No          | 在用户界面中显示的函数参数名称 |
| WorkflowFunctionParameterDataTypeId      | int           | No          | 与工作流函数参数关联的数据类型的唯一标识符 |
| WorkflowParameterDataTypeName            | nvarchar(50)  | No          | 与工作流函数参数关联的数据类型的名称 |
| ContractActionParameterValue             | nvarchar(255) | No          | 存储在智能合同中的参数值 |
| BlockHash                                | nvarchar(255) | 是         | 块的哈希 |
| BlockNumber                              | int           | 是         | 账本中的块数 |
| BlockTimestamp                           | datetime(2,7) | 是         | 块的时间戳 |
| TransactionId                            | int           | No          | 事务的唯一标识符 |
| TransactionFrom                          | nvarchar(255) | 是         | 发起事务的一方 |
| TransactionTo                            | nvarchar(255) | 是         | 事务的另一方 |
| TransactionHash                          | nvarchar(255) | 是         | 事务的哈希 |
| TransactionIsWorkbenchTransaction        | bit           | 是         | A bit that identifies if the transaction is an Azure Blockchain Workbench transaction |
| TransactionProvisioningStatus            | int           | 是         | 标识事务的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建事务<br />1 – 事务已发送到账本<br />2 – 事务已成功部署到账本                 |
| TransactionValue                         | decimal(32,2) | 是         | 事务的值 |

## <a name="vwcontractproperty"></a>vwContractProperty

此视图呈现与合同关联的属性相关的大部分信息，方便促成常见的报告方案。 对于采用的每个属性，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   部署工作流的用户的用户详细信息
-   关联的智能合同属性定义
-   属性的特定实例值
-   合同的状态属性的详细信息

| 名称                               | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                    | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName             | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                 | bit           | No          | 标识当前是否已启用应用程序。<br />**注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。                      |
| WorkflowId                         | int           | No          | 工作流的唯一标识符 |
| WorkflowName                       | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName                | nvarchar(255) | No          | 在用户界面中显示的工作流名称 |
| WorkflowDescription                | nvarchar(255) | 是         | 工作流的说明 |
| ContractId                         | int           | No          | 合同的唯一标识符 |
| ContractProvisioningStatus         | int           | No          | 标识合同的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建合同<br />1 – 合同已发送到账本<br />2 – 合同已成功部署到账本<br />3 或 4 - 合同无法部署到账本<br />5 – 合同已成功部署到账本 <br /><br />从版本 1.5 开始，支持值 0 到 5。 为了在当前版本中向后兼容，提供了仅支持值 0 到 2 的视图 **vwContractPropertyV0**。 |
| ContractCodeId                     | int           | No          | 合同代码实现的唯一标识符 |
| ContractLedgerIdentifier           | nvarchar(255) | 是         | 与特定分布式账本的已部署智能合同版本关联的唯一标识符。 例如 Ethereum。 |
| ContractDeployedByUserId           | int           | No          | 部署合同的用户的唯一标识符 |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 是         | 部署合同的用户的名字 |
| ContractDeployedByUserLastName     | nvarchar(255) | 是         | 部署合同的用户的姓氏 |
| ContractDeployedByUserExternalId   | nvarchar(255) | No          | 部署合同的用户的外部标识符。 By default, this ID is the guid that represents their identity in the consortium Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 是         | 部署合同的用户的电子邮件地址 |
| WorkflowPropertyId                 | int           |             | 工作流的属性的唯一标识符 |
| WorkflowPropertyDataTypeId         | int           | No          | 属性的数据类型的 ID |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | No          | 属性的数据类型的名称 |
| WorkflowPropertyName               | nvarchar(50)  | No          | 工作流属性的名称 |
| WorkflowPropertyDisplayName        | nvarchar(255) | No          | 工作流属性的显示名称 |
| WorkflowPropertyDescription        | nvarchar(255) | 是         | 属性的说明 |
| ContractPropertyValue              | nvarchar(255) | No          | 合同中属性的值 |
| StateName                          | nvarchar(50)  | 是         | If this property contains the state of the contract, it is the display name for the state. 如果此属性不与状态相关联，则值为 null。 |
| StateDisplayName                   | nvarchar(255) | No          | If this property contains the state, it is the display name for the state. 如果此属性不与状态相关联，则值为 null。 |
| StateValue                         | nvarchar(255) | 是         | If this property contains the state, it is the state value. 如果此属性不与状态相关联，则值为 null。 |

## <a name="vwcontractstate"></a>vwContractState

此视图呈现与特定合同状态相关的大部分信息，方便促成常见的报告方案。 此视图中的每条记录包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   部署工作流的用户的用户详细信息
-   关联的智能合同属性定义
-   合同的状态属性的详细信息

| 名称                               | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                    | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName             | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                 | bit           | No          | 标识当前是否已启用应用程序。<br />**注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。 |
| WorkflowId                         | int           | No          | 工作流的唯一标识符 |
| WorkflowName                       | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName                | nvarchar(255) | No          | 在用户界面中显示的名称 |
| WorkflowDescription                | nvarchar(255) | 是         | 工作流的说明 |
| ContractLedgerImplementationId     | nvarchar(255) | 是         | 与特定分布式账本的已部署智能合同版本关联的唯一标识符。 例如 Ethereum。 |
| ContractId                         | int           | No          | 合同的唯一标识符 |
| ContractProvisioningStatus         | int           | No          |标识合同的当前预配过程状态。 可能的值包括： <br />0 – API 已在数据库中创建合同<br />1 – 合同已发送到账本<br />2 – 合同已成功部署到账本<br />3 或 4 - 合同无法部署到账本<br />5 – 合同已成功部署到账本 <br /><br />从版本 1.5 开始，支持值 0 到 5。 为了在当前版本中向后兼容，提供了仅支持值 0 到 2 的视图 **vwContractStateV0**。 |
| ConnectionId                       | int           | No          | 工作流部署到的区块链实例的唯一标识符 |
| ContractCodeId                     | int           | No          | 合同代码实现的唯一标识符 |
| ContractDeployedByUserId           | int           | No          | 部署合同的用户的唯一标识符 |
| ContractDeployedByUserExternalId   | nvarchar(255) | No          | 部署合同的用户的外部标识符。 By default, this ID is the guid that represents their identity in the consortium Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | 是         | 部署合同的用户的名字 |
| ContractDeployedByUserLastName     | nvarchar(255) | 是         | 部署合同的用户的姓氏 |
| ContractDeployedByUserEmailAddress | nvarchar(255) | 是         | 部署合同的用户的电子邮件地址 |
| WorkflowPropertyId                 | int           | No          | 工作流属性的唯一标识符 |
| WorkflowPropertyDataTypeId         | int           | No          | 工作流属性的数据类型的 ID |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | No          | 工作流属性的数据类型的名称 |
| WorkflowPropertyName               | nvarchar(50)  | No          | 工作流属性的名称 |
| WorkflowPropertyDisplayName        | nvarchar(255) | No          | 在 UI 中显示的属性名称 |
| WorkflowPropertyDescription        | nvarchar(255) | 是         | 属性的说明 |
| ContractPropertyValue              | nvarchar(255) | No          | 合同中存储的属性值 |
| StateName                          | nvarchar(50)  | 是         | If this property contains the state, it the display name for the state. 如果此属性不与状态相关联，则值为 null。 |
| StateDisplayName                   | nvarchar(255) | No          | If this property contains the state, it is the display name for the state. 如果此属性不与状态相关联，则值为 null。 |
| StateValue                         | nvarchar(255) | 是         | If this property contains the state, it is the state value. 如果此属性不与状态相关联，则值为 null。 |

## <a name="vwuser"></a>vwUser

此视图提供有关预配为使用 Azure Blockchain Workbench 的联盟成员的详细信息。 默认情况下，会通过用户的初始预配填充数据。

| 名称               | Type          | 可为 Null | 描述                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | No          | 用户的唯一标识符 |
| ExternalID         | nvarchar(255) | No          | 用户的外部标识符。 By default, this ID is the guid representing the Azure Active Directory ID for the user. |
| ProvisioningStatus | int           | No          |标识用户的当前预配过程状态。 可能的值包括： <br />0 – API 已创建用户<br />1 – 已将密钥与数据库中的用户相关联<br />2 – 已完全预配用户 |
| FirstName          | nvarchar(50)  | 是         | 用户的名字 |
| LastName           | nvarchar(50)  | 是         | 用户的姓氏 |
| EmailAddress       | nvarchar(255) | 是         | 用户的电子邮件地址 |

## <a name="vwworkflow"></a>vwWorkflow

此视图呈现有关核心工作流元数据以及工作流函数和参数的详细信息。 Designed for reporting, it also contains metadata about the application associated with the workflow. 此视图包含来自多个基础表的数据，以促成工作流报告。 对于每个工作流，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   关联的工作流启动状态信息

| 名称                              | Type          | 可为 Null | 描述                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                   | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName            | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                | bit           | No          | 标识是否已启用应用程序 |
| WorkflowId                        | int           | 是         | 工作流的唯一标识符 |
| WorkflowName                      | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName               | nvarchar(255) | No          | 在用户界面中显示的名称 |
| WorkflowDescription               | nvarchar(255) | 是         | 工作流的说明。 |
| WorkflowConstructorFunctionId     | int           | No          | 充当工作流构造函数的工作流函数的标识符 |
| WorkflowStartStateId              | int           | No          | 状态的唯一标识符 |
| WorkflowStartStateName            | nvarchar(50)  | No          | 状态的名称 |
| WorkflowStartStateDisplayName     | nvarchar(255) | No          | 在用户界面中显示的状态名称 |
| WorkflowStartStateDescription     | nvarchar(255) | 是         | 工作流状态的说明 |
| WorkflowStartStateStyle           | nvarchar(50)  | 是         | 此值标识处于此状态的工作流的完成百分比 |
| WorkflowStartStateValue           | int           | No          | The value of the state |
| WorkflowStartStatePercentComplete | int           | No          | 一段文本说明，提示客户端如何在 UI 中呈现此状态。 支持的状态包括 *Success* 和 *Failure* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

此视图呈现有关核心工作流元数据以及工作流函数和参数的详细信息。 Designed for reporting, it also contains metadata about the application associated with the workflow. 此视图包含来自多个基础表的数据，以促成工作流报告。 对于每个工作流函数，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   工作流函数详细信息

| 名称                                 | Type          | 可为 Null | 描述                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | No          | 应用程序的唯一标识符 |
| ApplicationName                      | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName               | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled                   | bit           | No          | 标识是否已启用应用程序 |
| WorkflowId                           | int           | No          | 工作流的唯一标识符 |
| WorkflowName                         | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName                  | nvarchar(255) | No          | 在用户界面中显示的工作流名称 |
| WorkflowDescription                  | nvarchar(255) | 是         | 工作流的说明 |
| WorkflowFunctionId                   | int           | No          | 函数的唯一标识符 |
| WorkflowFunctionName                 | nvarchar(50)  | 是         | 函数的名称 |
| WorkflowFunctionDisplayName          | nvarchar(255) | No          | 在用户界面中显示的函数名称 |
| WorkflowFunctionDescription          | nvarchar(255) | 是         | 工作流函数的说明 |
| WorkflowFunctionIsConstructor        | bit           | No          | Identifies if the workflow function is the constructor for the workflow |
| WorkflowFunctionParameterId          | int           | No          | 函数的参数的唯一标识符 |
| WorkflowFunctionParameterName        | nvarchar(50)  | No          | 函数的参数的名称 |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | No          | 在用户界面中显示的函数参数名称 |
| WorkflowFunctionParameterDataTypeId  | int           | No          | 与工作流函数参数关联的数据类型的唯一标识符 |
| WorkflowParameterDataTypeName        | nvarchar(50)  | No          | 与工作流函数参数关联的数据类型的名称 |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

此视图呈现针对工作流定义的属性。 对于每个属性，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   工作流属性详细信息

| 名称                         | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | 应用程序的唯一标识符 |
| ApplicationName              | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName       | nvarchar(255) | No          | 在用户界面中显示的名称 |
| ApplicationEnabled           | bit           | No          | 标识当前是否已启用应用程序。<br />**注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。 |
| WorkflowId                   | int           | No          | 工作流的唯一标识符 |
| WorkflowName                 | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName          | nvarchar(255) | No          | 在用户界面中显示的工作流名称 |
| WorkflowDescription          | nvarchar(255) | 是         | 工作流的说明 |
| WorkflowPropertyID           | int           | No          | 工作流的属性的唯一标识符 |
| WorkflowPropertyName         | nvarchar(50)  | No          | 属性的名称 |
| WorkflowPropertyDescription  | nvarchar(255) | 是         | 属性的说明 |
| WorkflowPropertyDisplayName  | nvarchar(255) | No          | 在用户界面中显示的名称 |
| WorkflowPropertyWorkflowId   | int           | No          | 与此属性关联的工作流的 ID |
| WorkflowPropertyDataTypeId   | int           | No          | 为属性定义的数据类型的 ID |
| WorkflowPropertyDataTypeName | nvarchar(50)  | No          | 为属性定义的数据类型的名称 |
| WorkflowPropertyIsState      | bit           | No          | 此字段标识此工作流属性是否包含工作流的状态 |

## <a name="vwworkflowstate"></a>vwWorkflowState

此视图呈现与工作流关联的属性。 对于每个合同，此视图包含以下数据：

-   关联的应用程序定义
-   关联的工作流定义
-   工作流状态信息

| 名称                         | Type          | 可为 Null | 描述                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | 应用程序的唯一标识符 |
| ApplicationName              | nvarchar(50)  | No          | 应用程序的名称 |
| ApplicationDisplayName       | nvarchar(255) | No          | 应用程序的说明 |
| ApplicationEnabled           | bit           | No          | 标识当前是否已启用应用程序。<br />**注意：** 即使可以在数据库中将某个应用程序表示为已禁用，但关联的合同会保留在区块链中，并且有关这些合同的数据会保留在数据库中。 |
| WorkflowId                   | int           | No          | 工作流的唯一标识符 |
| WorkflowName                 | nvarchar(50)  | No          | 工作流的名称 |
| WorkflowDisplayName          | nvarchar(255) | No          | 在用户界面中显示的工作流名称 |
| WorkflowDescription          | nvarchar(255) | 是         | 工作流的说明 |
| WorkflowStateID              | int           | No          | 状态的唯一标识符 |
| WorkflowStateName            | nvarchar(50)  | No          | 状态的名称 |
| WorkflowStateDisplayName     | nvarchar(255) | No          | 在用户界面中显示的状态名称 |
| WorkflowStateDescription     | nvarchar(255) | 是         | 工作流状态的说明 |
| WorkflowStatePercentComplete | int           | No          | 此值标识处于此状态的工作流的完成百分比 |
| WorkflowStateValue           | nvarchar(50)  | No          | Value of the state |
| WorkflowStateStyle           | nvarchar(50)  | No          | 一段文本说明，提示客户端如何在 UI 中呈现此状态。 支持的状态包括 *Success* 和 *Failure* |
