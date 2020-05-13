---
title: Azure 中继 Node API 概述 | Microsoft Docs
description: 本文概述了用于 Azure 中继服务的 Node.js API。 它还演示了如何使用 hyco-ws Node 包。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211835"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>中继混合连接 Node API 概述

## <a name="overview"></a>概述

Azure 中继混合连接的 [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Node 包是在 [‘ws’](https://www.npmjs.com/package/ws) NPM 包的基础上进行构建和扩展的。 此包将重新导出该基程序包的所有导出，并添加允许与 Azure 中继服务混合连接功能集成的新导出。 

在现有应用程序中，`require('ws')` 可以改为结合使用此包与 `require('hyco-ws')`，从而还可以实现混合方案，其中应用程序可以同时从“防火墙内”和通过混合连接本地侦听 WebSocket 连接。
  
## <a name="documentation"></a>文档

API [记录于主要的 ‘ws’ 包中](https://github.com/websockets/ws/blob/master/doc/ws.md)。 本文介绍此包与该基线有何不同。 

基程序包和此 ‘hyco-ws’ 之间的主要区别是添加了新的服务器类、通过 `require('hyco-ws').RelayedServer` 导出以及添加了一些帮助程序方法。

### <a name="package-helper-methods"></a>包帮助程序方法

包导出上提供了几种实用方法，可按以下方式引用：

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

帮助程序方法可用于此包，但也可用于 Node 服务器，使 Web 或设备客户端能够创建侦听器或发件人。 服务器使用这些方法时，向它们传递嵌入生存期较短的令牌的 URI。 这些 URI 也可用于不支持设置 WebSocket 握手的 HTTP 头的常见 WebSocket 堆栈。 将授权令牌嵌入到 URI 主要针对这些库外使用方案。 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

为给定命名空间和路径创建有效的 Azure 中继混合连接侦听器 URI。 此 URI 随后可用于 WebSocketServer 类的中继版本。

- `namespaceName`（必需）- 要使用的 Azure 中继命名空间的域限定名称。
- `path`（必需）- 该命名空间中现有 Azure 中继混合连接的名称。
- `token`（可选）- 以前颁发的中继访问令牌，嵌入到侦听器 URI 中（请参阅以下示例）。
- `id`（可选）- 用于启用请求的端到端诊断跟踪的跟踪标识符。

`token` 值是可选的，仅在不可能发送 HTTP 头和 WebSocket 握手时使用，这与 W3C WebSocket 堆栈的情况相同。                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

为给定命名空间和路径创建有效的 Azure 中继混合连接发送 URI。 此 URI 可与任何 WebSocket 客户端配合使用。

- `namespaceName`（必需）- 要使用的 Azure 中继命名空间的域限定名称。
- `path`（必需）- 该命名空间中现有 Azure 中继混合连接的名称。
- `token`（可选）- 以前颁发的中继访问令牌，嵌入到发送 URI 中（请参阅以下示例）。
- `id`（可选）- 用于启用请求的端到端诊断跟踪的跟踪标识符。

`token` 值是可选的，仅在不可能发送 HTTP 头和 WebSocket 握手时使用，这与 W3C WebSocket 堆栈的情况相同。                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

为给定目标 URI、SAS 规则和 SAS 规则密钥创建 Azure 中继共享访问签名 (SAS) 令牌，其有效期为指定的数秒钟或者如果忽略到期参数，则为从当前时刻起一小时。

- `uri`（必需）- 为之颁发令牌的 URI。 规范化此 URI 以使用 HTTP 方案，且将除去查询字符串信息。
- `ruleName`（必需）- 由给定 URI 表示的实体的 SAS 规则名称，或者由 URI 主机部分表示的命名空间的 SAS 规则名称。
- `key`（必需）- SAS 规则的有效密钥。 
- `expirationSeconds`（可选）- 已生成的令牌过期之前的秒数。 如果未指定，则默认值为 1 小时 (3600)。

颁发的令牌在给定的时间段内授予指定的 SAS 规则相关的权限。

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

此方法在功能上等效于之前记录的 `createRelayToken` 方法，但返回正确追加到输入 URI 的令牌。

### <a name="class-wsrelayedserver"></a>Class ws.RelayedServer

`hycows.RelayedServer` 类可替代 `ws.Server` 类，不侦听本地网络，但委托侦听 Azure 中继服务。

这两个类通常为约定兼容，也就是说，使用 `ws.Server` 类的现有应用程序可以轻易改为使用中继版本。 主要差异在于构造函数和可用选项。

#### <a name="constructor"></a>构造函数  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer` 构造函数支持一组不同的参数，而不支持 `Server`，因为该函数既不是独立侦听器，也不能嵌入到现有 HTTP 侦听器框架中。 自从将 WebSocket 管理大幅委托到中继服务后，也有少数选项可用。

构造函数参数：

- `server`（必需）- 要侦听的混合连接名称的完全限定 URI，通常使用 WebSocket.createRelayListenUri() 帮助程序方法构造。
- `token`（必需）- 此参数可以保留以前颁发的令牌字符串，也可以保留为获得此类令牌字符串而调用的回调函数。 在启用令牌续订时，首选回调选项。

#### <a name="events"></a>事件

`RelayedServer` 实例将发出三个事件，使你能够处理传入的请求、建立连接，以及检测错误条件。 订阅 `connect` 事件后才能处理消息。 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

接受传入连接前将引发 `headers` 事件，可以实现将标头的修改发送到客户端。 

##### <a name="connection"></a>连接

```JavaScript
function(socket)
```

接受新的 WebSocket 连接时发出。 此对象的类型为 `ws.WebSocket`，与基程序包相同。


##### <a name="error"></a>error

```JavaScript
function(error)
```

如果基础服务器发出错误，将转发到此处。  

#### <a name="helpers"></a>帮助程序

为了简化中继服务器的启动和对传入连接的即时订阅，该包公开了一个简单的帮助程序函数，该函数也用于本示例中，如下所示：

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

此方法调用构造函数以创建 RelayedServer 的新实例，并订阅提供的 'connection' 事件的回调。
 
##### <a name="relayedconnect"></a>relayedConnect

只需在函数中生成 `createRelayedServer` 帮助程序的镜像，`relayedConnect` 将创建客户端连接，并订阅生成套接字上的 'open' 事件。

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请访问以下链接：
* [什么是 Azure 中继？](relay-what-is-it.md)
* [可用的中继 API](relay-api-overview.md)
