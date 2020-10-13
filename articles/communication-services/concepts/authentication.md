---
title: Azure 通信服务的身份验证
titleSuffix: An Azure Communication Services concept document
description: 了解应用或服务可对通信服务进行身份验证的各种方式。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 928737608ae3e3e44b352724713a284ff9a45da9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934513"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure 通信服务的身份验证

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

本文提供了有关使用 *访问密钥* 和 *用户访问令牌*对 Azure 通信服务的客户端进行身份验证的信息。 需要对每个与 Azure 通信服务的客户端交互进行身份验证。

下表介绍了 Azure 通信服务客户端库支持哪些身份验证选项：

| 客户端库 | 访问密钥    | 用户访问令牌 |
| -------------- | ------------- | ------------------ |
| 管理 | 支持     | 不支持      |
| SMS            | 支持     | 不支持      |
| 聊天           | 不支持 | 支持          |
| 调用        | 不支持 | 支持          |

下面简要介绍每个授权选项：

- 用于 SMS 和管理操作的**访问密钥**身份验证。 访问密钥身份验证适用于在受信任的服务环境中运行的应用程序。 若要使用访问密钥进行身份验证，客户端将生成 [基于哈希的方法身份验证代码 (HMAC) ](https://en.wikipedia.org/wiki/HMAC) ，并将其包括在 `Authorization` 每个 HTTP 请求的标头中。 有关详细信息，请参阅 [使用访问密钥进行身份验证](#authenticate-with-an-access-key)。
- 用于聊天和呼叫的**用户访问令牌**身份验证。 用户访问令牌允许客户端应用程序直接对 Azure 通信服务进行身份验证。 这些令牌是在你创建的服务器端令牌预配服务上生成的。 然后，将它们提供给使用令牌初始化聊天和调用客户端库的客户端设备。 有关详细信息，请参阅 [使用用户访问令牌进行身份验证](#authenticate-with-a-user-access-token)。

## <a name="authenticate-with-an-access-key"></a>使用访问密钥进行身份验证

访问密钥身份验证使用共享密钥为使用 SHA256 算法计算的每个 HTTP 请求生成 HMAC，并使用方案将其发送到 `Authorization` 标头中 `HMAC-SHA256` 。

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

使用访问密钥身份验证的 Azure 通信服务客户端库应使用资源的连接字符串来初始化。 如果不使用客户端库，则可以使用资源的访问密钥以编程方式生成 Hmac。 若要了解有关连接字符串的详细信息，请访问 [资源预配快速入门](../quickstarts/create-communication-resource.md)。

### <a name="sign-an-http-request"></a>对 HTTP 请求进行签名

如果你不使用客户端库向 Azure 通信服务 REST Api 发出 HTTP 请求，则需要以编程方式为每个 HTTP 请求创建 Hmac。 以下步骤介绍如何构造 Authorization 标头：

1. 为 `x-ms-date` 标头或标准 HTTP 标头中的请求指定协调世界时 (UTC) 时间戳 `Date` 。 此服务对此进行验证以防范某些安全攻击，包括重播攻击。
1. 使用 SHA256 算法对 HTTP 请求正文进行哈希处理，然后通过 `x-ms-content-sha256` 标头传递请求。
1. 通过串联 HTTP 谓词来构造要签名的字符串 (例如 `GET` `PUT` ，或) 、http 请求路径和的值， `Date` `Host` 以及 `x-ms-content-sha256` 采用以下格式的 http 标头：
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. 为在上一步中创建的 UTF-8 编码字符串生成 HMAC-256 签名。 接下来，将结果编码为 Base64。 请注意，还需要对存储帐户密钥进行 Base64 解码。 使用以下格式 (显示为伪代码) ：
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. 按如下所示指定授权标头：
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    其中 `<hmac-sha256-signature>` 是在上一步中计算的 HMAC。

## <a name="authenticate-with-a-user-access-token"></a>使用用户访问令牌进行身份验证

用户访问令牌允许客户端应用程序直接对 Azure 通信服务进行身份验证。 若要实现此目的，你应该设置一个可信服务，该服务对你的应用程序用户进行身份验证，并使用管理客户端库颁发用户访问令牌。 请访问 [客户端和服务器体系结构](./client-and-server-architecture.md) 概念文档，以了解有关体系结构注意事项的详细信息。

`CommunicationClientCredential`类包含用于向客户端库提供用户访问令牌凭据并管理其生命周期的逻辑。

### <a name="initialize-the-client-libraries"></a>初始化客户端库

若要初始化需要用户访问令牌身份验证的 Azure 通信服务客户端库，请首先创建类的实例 `CommunicationClientCredential` ，然后使用它来初始化 API 客户端。

以下代码片段演示了如何使用用户访问令牌初始化聊天客户端库：

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>正在刷新用户访问令牌

用户访问令牌是需要重新颁发的短期凭据，以防止用户遇到服务中断的情况。 `CommunicationUserCredential`构造函数接受刷新回调函数，使你能够在用户访问令牌过期之前对其进行更新。 应使用此回调从受信任的服务提取新的用户访问令牌。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

`refreshProactively`选项使你可以决定如何管理令牌生命周期。 默认情况下，当令牌过期时，回调会阻止 API 请求，并尝试对其进行刷新。 当设置为时，将在 `refreshProactively` `true` 令牌过期前计划并异步执行回调。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建用户访问令牌](../quickstarts/access-tokens.md)

有关详细信息，请参阅以下文章：
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
