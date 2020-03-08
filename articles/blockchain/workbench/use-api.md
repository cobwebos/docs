---
title: 使用 Azure 区块链工作台 REST Api
description: 有关如何使用 Azure 区块链工作台 Preview 的方案 REST API
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672748"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>使用 Azure 区块链工作台预览版 REST API

Azure 区块链工作台预览版 REST API 向开发人员和信息工作者提供一种方式来构建丰富的区块链应用程序集成。 本文重点介绍如何使用工作台 REST API 的几个方案。 例如，假设要创建一个自定义区块链客户端，该客户端允许已登录的用户查看其分配的区块链应用程序并与其交互。 客户端可以使用区块链工作台 API 查看协定实例，并对智能协定采取措施。

## <a name="blockchain-workbench-api-endpoint"></a>区块链工作台 API 终结点

区块链工作台 Api 可通过部署的终结点进行访问。 获取部署的 API 终结点 URL：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择 "**资源组**"。
1. 选择部署的区块链工作台的资源组名称。
1. 选择“类型”列标题，按类型的字母顺序将列表排序。
1. 有两个类型为“应用服务”的资源。 选择*类型为 "* api" 后缀的**应用服务**的资源。
1. 在应用服务**概述**中，将 " **URL** " 值复制到已部署的区块链工作台的 API 终结点 url。

    ![应用服务 API 终结点 URL](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

对区块链工作台 REST API 的请求受到 Azure Active Directory （Azure AD）的保护。

若要向 REST Api 发出经过身份验证的请求，客户端代码需要使用有效凭据进行身份验证，然后才能调用 API。 身份验证通过 Azure AD 在各种执行组件之间进行协调，并为客户端提供[访问令牌](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token)作为身份验证的证明。 然后在 REST API 请求的 HTTP 授权标头中发送令牌。 若要详细了解 Azure AD 身份验证，请参阅[开发人员 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。

有关如何进行身份验证的示例，请参阅[REST API 示例](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples)。

## <a name="using-postman"></a>使用 Postman

如果要测试或尝试使用工作台 Api，可以使用[Postman](https://www.postman.com)对部署进行 API 调用。 从 GitHub[下载工作台 API 请求的示例 Postman 集合](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman)。 有关身份验证和使用示例 API 请求的详细信息，请参阅自述文件。

## <a name="create-an-application"></a>创建应用程序

使用两个 API 调用创建区块链工作台应用程序。 此方法只能由作为工作台管理员的用户执行。

使用[应用程序 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost)上传应用程序的 JSON 文件并获取应用程序 ID。

### <a name="applications-post-request"></a>应用程序 POST 请求

使用**appFile**参数可以将配置文件作为请求正文的一部分发送。

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>应用程序 POST 响应

在响应中返回创建的应用程序 ID。 调用下一个 API 时，需要应用程序 ID 将配置文件与代码文件相关联。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>协定代码 POST 请求

通过传递应用程序 ID 来上传应用程序的密度代码文件，使用[应用程序协定代码 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) 。 有效负载可以是单个的 "密度文件"，也可以是包含 "密度" 文件的压缩文件。

请替换以下值：

| 参数 | 值 |
|-----------|-------|
| ApplicationId | 从应用程序 POST API 返回值。 |
| {ledgerId} | 分类帐的索引。 该值通常为1。 还可以检查[分类帐表](data-sql-management-studio.md)中是否有值。 |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>协定代码后响应

如果成功，响应将包含[ContractCode 表](data-sql-management-studio.md)中创建的协定代码 ID。

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>向用户分配角色

通过传递应用程序 ID、用户 ID 和应用程序角色 ID，使用[应用程序角色分配 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost)在指定的区块链应用程序中创建用户到角色的映射。 此方法只能由作为工作台管理员的用户执行。

### <a name="role-assignments-post-request"></a>角色分配 POST 请求

请替换以下值：

| 参数 | 值 |
|-----------|-------|
| ApplicationId | 从应用程序 POST API 返回值。 |
| Id | [用户表](data-sql-management-studio.md)中的用户 ID 值。 |
| {applicationRoleId} | 与[ApplicationRole 表](data-sql-management-studio.md)中的应用程序 ID 相关联的应用程序角色 id 值。 |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>角色分配后响应

如果成功，响应将包含[RoleAssignment 表](data-sql-management-studio.md)中创建的角色分配 ID。

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>列出应用程序

使用[应用程序获取 API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget)检索用户的所有区块链工作台应用程序。 在此示例中，已登录的用户可以访问两个应用程序：

- [资产转移](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [冷藏运输](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>应用程序获取请求

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>应用程序获取响应

响应会列出可供用户在 Blockchain Workbench 中访问的所有区块链应用程序。 Blockchain Workbench 管理员获取所有区块链应用程序。 非工作台管理员获取所有区块链应用程序，这些应用程序至少具有一个关联的应用程序角色或关联的智能协定实例角色。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>列出应用程序的工作流

使用[应用程序工作流获取 API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget)来列出用户在区块链工作台中具有访问权限的指定区块链应用程序的所有工作流。 每个区块链应用程序有一个或多个工作流，每个工作流有零个或多个智能合同实例。 对于只有一个工作流的区块链客户端应用程序，建议跳过允许用户选择相应工作流的用户体验流。

### <a name="application-workflows-request"></a>应用程序工作流请求

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>应用程序工作流响应

Blockchain Workbench 管理员获取所有区块链工作流。 非 Workbench 管理员获取至少具有一个关联的应用程序角色或与智能合同实例角色关联的全部工作流。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>创建协定实例

使用[约定 V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost)为工作流创建新的智能协定实例。 如果用户与应用程序角色关联，而用户可以启动工作流的智能协定实例，则用户只能创建新的智能协定实例。

> [!NOTE]
> 在此示例中，使用了 API 的版本2。 版本2协定 Api 为关联的 ProvisioningStatus 字段提供更多粒度。

### <a name="contracts-post-request"></a>合同 POST 请求

请替换以下值：

| 参数 | 值 |
|-----------|-------|
| 工作 | Workflow ID 值是来自[工作流表](data-sql-management-studio.md)的合同 ConstructorID。 |
| {contractCodeId} | [ContractCode 表](data-sql-management-studio.md)中的协定代码 ID 值。 关联要创建的约定实例的应用程序 ID 和分类帐 ID。 |
| ConnectionId | [连接表](data-sql-management-studio.md)中的连接 ID 值。 |

对于请求正文，使用以下信息设置值：

| 参数 | 值 |
|-----------|-------|
| workflowFunctionID | [WorkflowFunction 表](data-sql-management-studio.md)中的 ID。 |
| workflowActionParameters | 传递给构造函数的参数的名称值对。 对于每个参数，请使用[WorkflowFunctionParameter](data-sql-management-studio.md)表中的 workflowFunctionParameterID 值。 |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>协定 POST 响应

如果成功，角色分配 API 将从[ContractActionParameter 表](data-sql-management-studio.md)中返回 ContractActionID。

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>列出工作流的智能合同实例

使用[约定 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget)显示工作流的所有智能协定实例。 也可以允许用户深入了解任何显示的智能合同实例。

### <a name="contracts-request"></a>协定请求

在此示例中，设想用户会与某个需执行操作的智能合同实例交互。

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>协定响应

响应列出指定工作流的所有智能协定实例。 Workbench 管理员获取所有智能合同实例。 非 Workbench 管理员获取至少具有一个关联的应用程序角色或与智能合同实例角色关联的全部智能合同实例。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>列出可用于合同的操作

使用 "[协定操作获取 API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) " 显示给定协定状态的可用用户操作。 

### <a name="contract-action-request"></a>协定操作请求

在此示例中，用户正在查看其创建的新智能协定的所有可用操作。

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>协定操作响应

响应会列出用户可以根据指定智能合同实例的当前状态执行的所有操作。

* 修改：允许用户修改资产的说明和价格。
* 终止：允许用户终止资产合同。

如果用户有一个相关联的应用程序角色，或者与某个智能合同实例角色相关联，则用户可以获取所有适用于指定智能合同实例当前状态的操作。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>执行适用于合同的操作

使用[约定操作 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost)对指定的智能协定实例采取措施。

### <a name="contract-action-post-request"></a>协定操作 POST 请求

在这种情况下，请考虑这样的情况：用户想要修改资产的描述和价格。

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

用户只能根据指定智能合同实例的当前状态以及用户的关联应用程序角色或智能合同实例角色来执行操作。

### <a name="contract-action-post-response"></a>协定操作后响应

如果 POST 成功，则会返回没有响应正文的“HTTP 200 正常”响应。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>后续步骤

有关区块链工作台 Api 的参考信息，请参阅[Azure 区块链工作台 REST API 参考](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)。
