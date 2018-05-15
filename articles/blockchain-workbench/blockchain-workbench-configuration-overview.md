---
title: Azure Blockchain Workbench 配置参考
description: Azure Blockchain Workbench 应用程序配置概述。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 配置参考

 Azure Blockchain Workbench 应用程序是由配置元数据和智能合同代码定义的多方工作流。 配置元数据定义区块链应用程序的高级工作流和交互模型。 智能合同定义区块链应用程序的业务逻辑。 Workbench 使用配置和智能合同代码生成区块链应用程序用户体验。

配置元数据指定每个区块链应用程序的以下信息： 

* 区块链应用程序的名称和说明
* 可以操作或参与区块链应用程序的用户的独特角色
* 一个或多个工作流。 每个工作流充当用于控制业务逻辑流的状态机。 工作流可以是独立的，也可以彼此交互。

定义的每个工作流指定以下设置：

* 工作流的名称和说明
* 工作流的状态。  每种状态是业务逻辑控制流中的一个阶段。 
* 用于转换到下一状态的操作
* 有权启动每个操作的用户角色
* 表示代码文件中业务逻辑的智能合同

## <a name="application"></a>Application

区块链应用程序包含配置元数据、工作流，以及可以操作或参与应用程序的用户角色。

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| ApplicationName | 唯一的应用程序名称。 相应的智能合同必须对适用的合同类使用相同的 **ApplicationName**。  | 是 |
| DisplayName | 应用程序的友好显示名称。 | 是 |
| 说明 | 应用程序的说明。 | 否 |
| ApplicationRoles | [ApplicationRoles](#application-roles) 的集合。 可以操作或参与应用程序的用户角色。  | 是 |
| 工作流 | [工作流](#workflows)的集合。 每个工作流充当用于控制业务逻辑流的状态机。 | 是 |

有关示例，请参阅[配置文件示例](#configuration-file-example)。

## <a name="workflows"></a>工作流

可将应用程序的业务逻辑建模为状态机，在其中执行某些操作会导致业务逻辑流从一种状态转为另一种状态。 工作流是此类状态和操作的集合。 每个工作流包括一个或多个智能合同，这些合同表示代码文件中的业务逻辑。 可执行合同是工作流的实例。

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| 名称 | 唯一的工作流名称。 相应的智能合同必须对适用的合同类使用相同的 **Name**。 | 是 |
| DisplayName | 工作流的友好显示名称。 | 是 |
| 说明 | 工作流的说明。 | 否 |
| Initiators | [ApplicationRoles](#application-roles) 的集合。 为有权在工作流中创建合同的用户分配的角色。 | 是 |
| StartState | 工作流初始状态的名称。 | 是 |
| 属性 | [标识符](#identifiers)的集合。 表示可在链外读取，或者在用户体验工具中可视化的数据。 | 是 |
| 构造函数 | 定义用于创建工作流实例的输入参数。 | 是 |
| 函数 | 可在工作流中执行的[函数](#functions)集合。 | 是 |
| States | 工作流[状态](#states)的集合。 | 是 |

有关示例，请参阅[配置文件示例](#configuration-file-example)。

## <a name="type"></a>Type

支持的数据类型

| Type | 说明 |
|-------|-------------|
| 地址  | 区块链地址类型，例如 *contracts* 或 *users* |
| bool     | 布尔数据类型 |
| contract | 类型合同的地址 |
| int      | 整数数据类型 |
| money    | 货币数据类型 |
| state    | 工作流状态 |
| 字符串   | 字符串数据类型 |
| user     | 类型用户的地址 |
| time     | 时间数据类型 |
|`[ Application Role Name ]`| 应用程序角色中指定的任何名称。 将用户限制为使用该角色类型。 |

## <a name="constructor"></a>构造函数

定义工作流实例的输入参数。

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| parameters | 启动智能合同所需的[标识符](#identifiers)集合。 | 是 |

### <a name="constructor-example"></a>构造函数示例

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>函数

定义可在工作流中执行的函数。

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| 名称 | 函数的唯一名称。 相应的智能合同必须对适用的函数使用相同的 **Name**。 | 是 |
| DisplayName | 函数的友好显示名称。 | 是 |
| 说明 | 函数的说明 | 否 |
| parameters | 对应于函数参数的[标识符](#identifiers)集合。 | 是 |

### <a name="functions-example"></a>函数示例

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

工作流中唯一状态的集合。 每种状态捕获业务逻辑控制流中的一个步骤。 

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| 名称 | 状态的唯一名称。 相应的智能合同必须对适用的状态使用相同的 **Name**。 | 是 |
| DisplayName | 状态的友好显示名称。 | 是 |
| 说明 | 状态的说明。 | 否 |
| PercentComplete | 在 Blockchain Workbench 用户界面中显示的整数值，用于显示业务逻辑控制流中的进度。 | 是 |
| Style | 视觉提示，指示状态是表示成功还是失败。 有两个有效值：`Success` 或 `Failure`。 | 是 |
| Transitions | 从当前状态到下一组状态的可用[转换](#transitions)集合。 | 否 |

### <a name="states-example"></a>状态示例

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

用于转换到下一状态的操作。 一个或多个用户角色可在每种状态下执行某个操作，其中的操作可将工作流中的一种状态转换为另一种状态。 

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| AllowedRoles | 有权启动转换的应用程序角色列表。 具有指定角色的所有用户可以执行操作。 | 否 |
| AllowedInstanceRoles | 智能合同中参与或指定的、有权启动转换的用户角色列表。 实例角色在工作流中的“属性”内定义。 这些用户表示智能合同中参与或指定的用户，而不是具有某种角色类型的所有用户。 | 否 |
| DisplayName | 转换的友好显示名称。 | 是 |
| 说明 | 转换的说明。 | 否 |
| 函数 | 用于启动转换的函数的名称。 | 是 |
| NextStates | 成功转换后的潜在后续状态集合。 | 是 |

### <a name="transitions-example"></a>转换示例

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>应用程序角色

应用程序角色定义一组角色，这些角色可分配到想要操作或参与应用程序的用户。 应用程序角色可用于限制区块链应用程序和相应工作流中的操作和参与。 

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| 名称 | 应用程序角色的唯一名称。 相应的智能合同必须对适用的角色使用相同的 **Name**。 基类型名称被系统保留。 不能使用 [Type](#type) 的名称来命名应用程序角色| 是 |
| 说明 | 应用程序角色的说明。 | 否 |

### <a name="application-roles-example"></a>应用程序角色示例

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>标识符

标识符表示用于描述工作流属性、构造函数和函数参数的信息集合。 

| 字段 | 说明 | 必选 |
|-------|-------------|:--------:|
| 名称 | 属性或参数的唯一名称。 相应的智能合同必须对适用的属性或参数使用相同的 **Name**。 | 是 |
| DisplayName | 属性或参数的友好显示名称。 | 是 |
| 说明 | 属性或参数的说明。 | 否 |

### <a name="identifiers-example"></a>标识符示例

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>配置文件示例

以下示例定义一个基本的请求-响应应用程序，其中的请求方发送请求，响应方发送对请求的响应。

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>后续步骤

[部署 Azure Blockchain Workbench](blockchain-workbench-deploy.md)

