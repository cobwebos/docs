---
title: Azure Blockchain Workbench 消息集成概述
description: 有关在 Azure Blockchain Workbench 中使用消息的概述。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614355"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 消息集成

除了提供 REST API 以外，Azure Blockchain Workbench 还提供基于消息的集成。 Workbench 通过 Azure 事件网格发布以账本为中心的事件，使下游使用者能够根据这些事件引入数据或执行操作。 对于需要可靠消息传递的客户端，Azure Blockchain Workbench 还会将消息传送到 Azure 服务总线终结点。

## <a name="input-apis"></a>输入 API

若要从外部系统发起事务以创建用户、创建合同和更新合同，可以使用消息传送输入 API 针对账本执行事务。 有关演示输入 API 的示例，请参阅[消息传送集成示例](https://aka.ms/blockchain-workbench-integration-sample)。

下面是目前可用的输入 API。

### <a name="create-user"></a>创建用户

创建新用户。

该请求需要填写以下字段：

| **名称**             | **说明**                                      |
|----------------------|------------------------------------------------------|
| requestId            | 客户端提供的 GUID                                |
| firstName            | 用户的名字                              |
| lastName             | 用户的姓氏                               |
| emailAddress         | 用户的电子邮件地址                           |
| externalId           | 用户的 Azure AD 对象 ID                      |
| connectionId         | 区块链连接的唯一标识符 |
| messageSchemaVersion | 消息传送架构版本                            |
| messageName          | **CreateUserRequest**                               |

示例：

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench 返回包含以下字段的响应：

| **名称**              | **说明**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | 客户端提供的 GUID |
| userId                | 创建的用户的 ID |
| userChainIdentifier   | 在区块链网络上创建的用户的地址。 在 Ethereum 中，地址是用户的 **链上** 地址。 |
| connectionId          | 区块链连接的唯一标识符|
| messageSchemaVersion  | 消息传送架构版本 |
| messageName           | **CreateUserUpdate** |
| status                | 用户创建请求的状态。  如果成功，则值为 **Success**。 如果失败，则值为 **Failure**。     |
| additionalInformation | 基于状态提供的附加信息 |

Blockchain Workbench 返回的 **create user** 成功响应示例：

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

如果请求不成功，则会在附加信息中包含有关失败的详细信息。

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>创建合同

创建新合同。

该请求需要填写以下字段：

| **名称**             | **说明**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | 客户端提供的 GUID |
| userChainIdentifier  | 在区块链网络上创建的用户的地址。 在 Ethereum 中，此地址是用户的**链上**地址。 |
| applicationName      | 应用程序的名称 |
| workflowName         | 工作流的名称 |
| parameters           | 合同创建请求的参数输入 |
| connectionId         | 区块链连接的唯一标识符 |
| messageSchemaVersion | 消息传送架构版本 |
| messageName          | **CreateContractRequest** |

示例：

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench 返回包含以下字段的响应：

| **名称**                 | **说明**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | 客户端提供的 GUID                                                             |
| contractId               | Azure Blockchain Workbench 中的合同的唯一标识符 |
| contractLedgerIdentifier | 账本中合同的地址                                            |
| connectionId             | 区块链连接的唯一标识符                               |
| messageSchemaVersion     | 消息传送架构版本                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | 合同创建请求的状态。  可能的值：**Submitted**、**Committed**、**Failure**。  |
| additionalInformation    | 基于状态提供的附加信息                              |

Blockchain Workbench 返回的页面提交 **create contract** 响应示例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Blockchain Workbench 返回的事务提交 **create contract** 响应示例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

如果请求不成功，则会在附加信息中包含有关失败的详细信息。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>创建合同操作

创建新的合同操作。

该请求需要填写以下字段：

| **名称**                 | **说明**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | 客户端提供的 GUID |
| userChainIdentifier      | 在区块链网络上创建的用户的地址。 在 Ethereum 中，此地址是用户的**链上**地址。 |
| contractLedgerIdentifier | 账本中合同的地址 |
| workflowFunctionName     | 工作流函数的名称 |
| parameters               | 合同创建请求的参数输入 |
| connectionId             | 区块链连接的唯一标识符 |
| messageSchemaVersion     | 消息传送架构版本 |
| messageName              | **CreateContractActionRequest** |

示例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench 返回包含以下字段的响应：

| **名称**              | **说明**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | 客户端提供的 GUID|
| contractId            | Azure Blockchain Workbench 中的合同的唯一标识符 |
| connectionId          | 区块链连接的唯一标识符 |
| messageSchemaVersion  | 消息传送架构版本 |
| messageName           | **CreateContractActionUpdate** |
| status                | 合同操作请求的状态。 可能的值：**Submitted**、**Committed**、**Failure**。                         |
| additionalInformation | 基于状态提供的附加信息 |

Blockchain Workbench 返回的页面提交 **create contract action** 响应示例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Blockchain Workbench 返回的事务提交 **create contract action** 响应示例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

如果请求不成功，则会在附加信息中包含有关失败的详细信息。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>输入 API 错误代码和消息

**错误代码 4000：错误的请求错误**
- connectionId 无效
- CreateUserRequest 反序列化失败
- CreateContractRequest 反序列化失败
- CreateContractActionRequest 反序列化失败
- 应用程序 {由应用程序名称标识} 不存在
- 应用程序 {由应用程序名称标识} 没有工作流
- UserChainIdentifier 不存在
- 合同 {由账本标识符标识} 不存在
- 合同 {由账本标识符标识} 没有函数 {工作流函数名称}
- UserChainIdentifier 不存在

**错误代码 4090：冲突错误**
- 用户已存在
- 合同已存在
- 合同操作已存在

**错误代码 5000：内部服务器错误**
- 异常消息

## <a name="event-notifications"></a>事件通知

可以使用事件通知将 Blockchain Workbench 中发生的事件及其连接到的区块链网络告知用户和下游系统。 可以直接在代码中使用，或者结合逻辑应用和 Flow 等工具使用事件通知来触发到下游系统的数据流。

有关可接收的各种消息的详细信息，请参阅[通知消息参考](#notification-message-reference)。

### <a name="consuming-event-grid-events-with-azure-functions"></a>配合 Azure Functions 使用事件网格事件

如果用户想要使用事件网格来接收有关 Blockchain Workbench 中所发生的事件的通知，你可以通过 Azure Functions 使用来自事件网格的事件。

1. 在 Azure 门户中创建一个 **Azure 函数应用**。
2. 创建新函数。
3. 找到事件网格的模板。 其中会显示用于读取消息的基本模板代码。 根据需要修改代码。
4. 保存函数。 
5. 从 Blockchain Workbench 的资源组中选择“事件网格”。

### <a name="consuming-event-grid-events-with-logic-apps"></a>配合逻辑应用使用事件网格事件

1.  在 Azure 门户中创建一个新的 **Azure 逻辑应用**。
2.  在门户中打开 Azure 逻辑应用时，系统会提示选择触发器。 选择“Azure 事件网格 - 发生资源事件时”。
3. 显示工作流设计器后，系统会提示登录。
4. 选择订阅。 资源为 **Microsoft.EventGrid.Topics**。 从 Azure Blockchain Workbench 资源组的资源名称中选择“资源名称”。
5. 从 Blockchain Workbench 的资源组中选择“事件网格”。

## <a name="using-service-bus-topics-for-notifications"></a>使用服务总线主题发送通知

可以使用服务总线主题通知用户有关 Blockchain Workbench 中发生的事件。 

1.  浏览到 Workbench 资源组中的“服务总线”。
2.  选择“主题”。
3.  选择“workbench-external”。
4.  创建此主题的新订阅。 获取该订阅的密钥。
5.  创建一个程序，用于订阅此订阅的事件。

### <a name="consuming-service-bus-messages-with-logic-apps"></a>配合逻辑应用使用服务总线消息

1. 在 Azure 门户中创建一个新的 **Azure 逻辑应用**。
2. 在门户中打开 Azure 逻辑应用时，系统会提示选择触发器。 在搜索框中键入“服务总线”，然后选择适合与服务总线之间的交互类型的触发器。 例如，选择“服务总线 - 主题订阅中收到邮件时(自动完成)”。
3. 显示工作流设计器后，指定服务总线的连接信息。
4. 选择订阅并指定 **workbench-external** 主题。
5. 开发应用程序的逻辑，以利用此触发器的消息。

## <a name="notification-message-reference"></a>通知消息参考

根据 OperationName，通知消息采用以下消息类型之一。

### <a name="accountcreated"></a>AccountCreated

指示已请求将新帐户添加到指定的链。

| 名称    | Description  |
|----------|--------------|
| UserId  | 创建的用户的 ID。 |
| ChainIdentifier | 在区块链网络上创建的用户的地址。 在 Ethereum 中，此值为用户的“链中”地址。 |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

指示已发出在分布式账本中插入或更新合同的请求。

| 名称 | Description |
|-----|--------------|
| ChainID | 与请求关联的链的唯一标识符 |
| BlockId | 账本中块的唯一标识符 |
| ContractId | 合同的唯一标识符 |
| ContractAddress |       账本中合同的地址 |
| TransactionHash  |     账本中事务的哈希 |
| OriginatingAddress |   事务发起方的地址 |
| ActionName       |     操作的名称 |
| IsUpdate        |      标识此操作是否为更新 |
| parameters       |     一个对象列表，用于标识发送到操作的参数的名称、值和数据类型 |
| TopLevelInputParams |  如果已将某个合同连接到其他一个或多个合同，则这些项是来自顶级合同的参数。 |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

指示已发出针对分布式账本中特定合同执行操作的请求。

| 名称                     | Description                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | 此合同操作的唯一标识符 |
| ChainIdentifier          | 链的唯一标识符 |
| ConnectionId             | 连接的唯一标识符 |
| UserChainIdentifier      | 在区块链网络上创建的用户的地址。 在 Ethereum 中，此地址是用户的**链上**地址。 |
| ContractLedgerIdentifier | 账本中合同的地址 |
| WorkflowFunctionName     | 工作流函数的名称 |
| WorkflowName             | 工作流的名称 |
| WorkflowBlobStorageURL   | Blob 存储中合同的 URL |
| ContractActionParameters | 合同操作的参数 |
| TransactionHash          | 账本中事务的哈希 |
| 预配状态      | 操作的当前预配状态。</br>0 – 已创建</br>1 – 正在处理</br>2 – 完成</br> “完成”表示账本已确认成功添加此项 |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

指示已发出在特定分布式账本中更新用户余额的请求。

> [!NOTE]
> 只会针对需要帐户投资的账本生成此消息。
> 

| 名称    | Description                              |
|---------|------------------------------------------|
| 地址 | 已投资用户的地址 |
| Balance | 用户余额         |
| ChainID | 链的唯一标识符     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

消息指示已发出在分布式账本中添加块的请求。

| 名称           | Description                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | 要在其中添加块的链的唯一标识符             |
| BlockId        | Azure Blockchain Workbench 中的块的唯一标识符 |
| BlockHash      | 块的哈希                                                 |
| BlockTimeStamp | 块的时间戳                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

消息提供有关在分布式账本中添加事务的请求的详细信息。

| 名称            | Description                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | 要在其中添加块的链的唯一标识符             |
| BlockId         | Azure Blockchain Workbench 中的块的唯一标识符 |
| TransactionHash | 事务的哈希                                           |
| 源            | 事务发起方的地址                      |
| 目标              | 事务的目标接收方的地址              |
| 值           | 在事务中包含的值                                 |
| IsAppBuilderTx  | 标识这是否为 Blockchain Workbench 事务                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

提供有关合同的链标识符分配的详细信息。 例如，Ethereum 区块链中的账本上某个合同的地址。

| 名称            | Description                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Azure Blockchain Workbench 中的合同的唯一标识符 |
| ChainIdentifier | 链中合同的标识符                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>消息类型使用的类

### <a name="messagemodelbase"></a>MessageModelBase

所有消息的基础模型。

| 名称          | Description                          |
|---------------|--------------------------------------|
| OperationName | 操作的名称           |
| RequestId     | 请求的唯一标识符 |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

包含参数的名称、值和类型。

| 名称  | Description                 |
|-------|-----------------------------|
| 名称  | 参数的名称  |
| 值 | 参数值 |
| 类型  | 参数的类型  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

包含属性的 ID、名称、值和类型。

| 名称  | Description                |
|-------|----------------------------|
| ID    | 属性的 ID    |
| 名称  | 属性的名称  |
| 值 | 属性的值 |
| 类型  | 属性类型  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [智能合同集成模式](integration-patterns.md)