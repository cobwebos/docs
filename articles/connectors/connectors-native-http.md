---
title: "在逻辑应用中添加 HTTP 操作 | Microsoft Docs"
description: "使用属性的 HTTP 操作的概述"
services: 
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: d3514dad84bea024ad6215711877a9784a8d8ffd


---
# <a name="get-started-with-the-http-action"></a>HTTP 操作入门
借助 HTTP 操作，可以扩展组织的工作流并通过 HTTP 向任意终结点通信。

你可以：

* 创建在你管理的网站出现故障时激活（触发）的逻辑应用工作流。
* 通过 HTTP 向任意终结点通信以将工作流扩展到其他服务中。

若要开始在逻辑应用中使用 HTTP 操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="use-the-http-trigger"></a>使用 HTTP 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](connectors-overview.md)。

下面是显示如何在逻辑应用设计器中设置 HTTP 触发器的示例序列。

1. 在逻辑应用中添加 HTTP 触发器。
2. 为要轮询的 HTTP 终结点填写参数。
3. 修改关于轮询频率的重复周期间隔。
4. 逻辑应用现在可对每个检查期间返回的任何内容触发。

![HTTP 触发器](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>HTTP 触发器工作原理
HTTP 触发器按重复间隔对 HTTP 终结点进行调用。 默认情况下，小于 300 的任何 HTTP 响应代码都会导致应用运行。 可在代码视图中添加在 HTTP 调用后进行评估的条件，以确定逻辑应用是否应触发。 下面是一个每当返回的状态代码大于或等于 `400` 时触发的 HTTP 触发器示例。

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

[MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger) 上提供有关 HTTP 触发器参数的完整详细信息。

## <a name="use-the-http-action"></a>使用 HTTP 操作
操作是由在逻辑应用中定义的工作流执行的操作。 [了解有关操作的详细信息](connectors-overview.md)。

1. 选择“新步骤”按钮。
2. 选择“添加操作”。
3. 在操作搜索框中，键入 **http** 列出 HTTP 操作。
   
    ![选择 HTTP 操作](./media/connectors-native-http/using-action-1.png)
4. 添加 HTTP 调用所需的任何参数。
   
    ![完成 HTTP 操作](./media/connectors-native-http/using-action-2.png)
5. 单击工具栏左上角保存。 逻辑应用将保存并发布（激活）。

## <a name="http-trigger"></a>HTTP 触发器
下面是此连接器支持的触发器的详细信息。 HTTP 连接器具有一个触发器。

| 触发器 | 说明 |
| --- | --- |
| HTTP |进行 HTTP 调用并返回响应内容。 |

## <a name="http-action"></a>HTTP 操作
下面是此连接器支持的操作的详细信息。 HTTP 连接器具有一个可能的操作。

| 操作 | 说明 |
| --- | --- |
| HTTP |进行 HTTP 调用并返回响应内容。 |

## <a name="http-details"></a>HTTP 详细信息
下表介绍操作的必填和选填输入字段以及与使用该操作相关联的对应输出详细信息。

#### <a name="http-request"></a>HTTP 请求
下面是操作的输入字段，该操作发出 HTTP 出站请求。
* 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 方法* |方法 |要使用的 HTTP 谓词 |
| URI* |uri |HTTP 请求的 URI |
| 标头 |headers |要包含的 HTTP 标头的 JSON 对象 |
| 正文 |body |HTTP 请求正文 |
| 身份验证 |authentication |[身份验证](#authentication)部分中的详细信息 |

<br>

#### <a name="output-details"></a>输出详细信息
下面是 HTTP 响应的输出详细信息。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 标头 |对象 |响应标头 |
| 正文 |对象 |响应对象 |
| 状态代码 |int |HTTP 状态代码 |

## <a name="authentication"></a>身份验证
通过逻辑应用功能可对 HTTP 终结点使用不同类型的身份验证。 可将此身份验证用于 **HTTP**、**[HTTP + Swagger](connectors-native-http-swagger.md)** 和 **[HTTP Webhook](connectors-native-webhook.md)** 连接器。 可配置以下类型的身份验证：

* [基本身份验证](#basic-authentication)
* [客户端证书身份验证](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth 身份验证](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>基本身份验证
基本身份验证需要以下身份验证对象。
* 表示它是必填字段。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 类型* |type |身份验证类型（对于基本身份验证，必须是 `Basic`） |
| Username* |username |要进行身份验证的用户名 |
| 密码* |password |要进行身份验证的密码 |

> [!TIP]
> 如果要使用无法从定义检索的密码，则使用 `securestring` 参数和 `@parameters()` [工作流定义函数](http://aka.ms/logicappdocs)。
> 
> 

因此，将在身份验证字段中创建如下对象：

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>客户端证书身份验证
客户端证书身份验证需要以下身份验证对象。 * 表示它是必填字段。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 类型* |type |身份验证类型（对于 SSL 客户端证书，必须是 `ClientCertificate`） |
| PFX* |pfx |个人信息交换 (PFX) 文件的 Base64 编码内容 |
| 密码* |password |用于访问 PFX 文件的密码 |

> [!TIP]
> 可使用 `securestring` 参数和 `@parameters()` [工作流定义函数](http://aka.ms/logicappdocs)使用将在保存逻辑应用后不可读的参数。
> 
> 

例如：

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth 身份验证
Azure AD OAuth 身份验证需要以下身份验证对象。 * 表示它是必填字段。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 类型* |type |身份验证类型（对于 Azure AD OAuth，必须是 `ActiveDirectoryOAuth`） |
| 租户* |tenant |Azure AD 租户的租户标识符 |
| 受众* |audience |设置为 `https://management.core.windows.net/` |
| 客户端 ID* |clientId |Azure AD 应用程序的客户端标识符 |
| 机密* |secret |正在请求令牌的客户端的机密 |

> [!TIP]
> 可使用 `securestring` 参数和 `@parameters()` [工作流定义函数](http://aka.ms/logicappdocs)使用将在保存后不可读的参数。
> 
> 

例如：

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>后续步骤
现在，试用平台并[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。




<!--HONumber=Jan17_HO3-->


