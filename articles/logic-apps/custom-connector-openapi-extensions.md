---
title: "自定义连接器的 OpenAPI 扩展 - Azure 逻辑应用 | Microsoft Docs"
description: "使用高级功能扩展自定义连接器的 OpenAPI"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>使用高级功能扩展自定义连接器的 OpenAPI

若要创建 Azure 逻辑应用、Microsoft Flow 或 Microsoft PowerApps 的自定义连接器，必须提供 OpenAPI 定义文件。该文件是一个描述 API 操作和参数的不区分语言的机器可读文档。 结合 OpenAPI 的现成可用功能，还可以在创建逻辑应用和 Flow 的自定义连接器时包含这些 OpenAPI 扩展：

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

下面是有关这些扩展的更多详细信息：

<a name="summary"></a>

## <a name="summary"></a>摘要

指定动作（操作）的标题。 </br>
适用于：操作 </br>
建议：对 `summary` 使用句首大写形式。 </br>
示例：“When an event is added to calendar”或“Send an email”

![每个操作的“摘要”](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

指定实体的标题。 </br>
适用于：参数、响应架构 </br>
建议：对 `x-ms-summary` 使用标题字首大写形式。 </br>
示例：“Calendar ID”、“Subject”、“Event Description”，等等

![每个实体的“x-ms-summary”](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

指定操作的功能或实体的格式和函数的详细说明。 </br>
适用于：操作、参数、响应架构 </br>
建议：对 `description` 使用句首大写形式。 </br>
示例：“This operation triggers when a new event is added to the calendar”、“Specify the subject of the mail”，等等

![每个操作或实体的“说明”](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

指定实体的用户可见性。 </br>
可能的值：`important`、`advanced` 和 `internal` </br>
适用于：操作、参数、架构

* 始终首先向用户显示 `important` 操作和参数。
* `advanced` 操作和参数隐藏在附加的菜单下。
* `internal` 操作和参数向用户隐藏。

> [!NOTE] 
> 对于 `internal` 和 `required` 参数，**必须**为这些参数提供默认值。

示例：“查看更多”菜单和“显示高级选项”菜单会隐藏 `advanced` 操作和参数。

![用于显示或隐藏操作和参数的“x-ms-visibility”](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

向用户显示填充的列表，使他们能够选择操作的输入参数。 </br>
适用于：参数 </br>
用法：将 `x-ms-dynamic-values` 对象添加到参数的定义。 有关示例，请参阅此 [OpenAPI 示例](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)。

![用于显示列表的“x-ms-dynamic-values”](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>x-ms-dynamic-values 的属性

| Name | 必需还是可选 | 说明 | 
| ---- | -------------------- | ----------- | 
| **operationID** | 必选 | 为填充列表而要调用的操作 | 
| **value-path** | 必选 | `value-collection` 内对象中引用参数值的路径字符串。 </br>如果未指定 `value-collection`，则将响应评估为数组。 | 
| **value-title** | 可选 | `value-collection` 内对象中引用值说明的路径字符串。 </br>如果未指定 `value-collection`，则将响应评估为数组。 | 
| **value-collection** | 可选 | 评估为响应有效负载中对象数组的路径字符串 | 
| **parameters** | 可选 | 一个对象，其属性指定调用 dynamic-values 操作所需的输入参数 | 
|||| 

以下示例显示 `x-ms-dynamic-values` 中的属性：

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>示例：到目前为止的所有 OpenAPI 扩展

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

指示当前参数或响应的架构是动态的。 此对象可以调用此字段的值定义的操作，动态发现架构，并显示用于收集用户输入的相应 UI 或显示可用字段。 

适用于：参数、响应

用法：将 `x-ms-dynamic-schema` 对象添加到请求参数或响应正文定义。 有关示例，请参阅此 [OpenAPI 示例](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json)。

此示例演示输入窗体如何根据用户从下拉列表中选择的项发生变化：

![动态参数的“x-ms-dynamic-schema”](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

此示例演示输出窗体如何根据用户从下拉列表中选择的项发生变化。 在此版本中，用户选择了“Cars”：

![选定项“Cars”的“x-ms-dynamic-schema-response”](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

在此版本中，用户选择了“Food”：

![选定项“Food”的“x-ms-dynamic-schema-response”](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>x-ms-dynamic-schema 的属性

| Name | 必需还是可选 | 说明 | 
| ---- | -------------------- | ----------- | 
| **operationID** | 必选 | 为提取架构而要调用的操作 | 
| **parameters** | 必选 | 一个对象，其属性指定调用 dynamic-schema 操作所需的输入参数 | 
| **value-path** |可选 | 一个路径字符串，引用包含架构的属性。 </br>如果未指定，则假设响应包含根对象属性中的架构。 | 
|||| 

下面是动态参数的示例：

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

下面是动态响应的示例：

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>后续步骤

* [描述自定义 API 和连接器](../logic-apps/custom-connector-api-postman-collection.md)
* [逻辑应用：注册连接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow：注册连接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)