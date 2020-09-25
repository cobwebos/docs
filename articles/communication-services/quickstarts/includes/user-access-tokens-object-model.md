---
title: include 文件
description: include 文件
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943491"
---
## <a name="object-model"></a>对象模型

用户访问令牌使你可以授权应用程序的用户直接连接到 Azure 通信服务资源。 你可以在服务器上生成这些令牌，将它们传递回客户端应用程序，然后使用它们初始化通信服务客户端库。 若要实现此目的，应构建一个可信的服务终结点，该终结点可以对用户进行身份验证，并颁发用户访问令牌。 此服务应维护应用程序用户标识和 Azure 通信服务用户之间的永久映射。

CommunicationIdentityClient 类提供所有与用户和访问令牌相关的功能。 使用连接字符串将其实例化，然后使用它来管理用户，并颁发访问令牌。

> [!WARNING]
> 令牌是敏感数据，因为令牌授予对用户资源的访问权限。 因此，保护令牌不被盗用至关重要。 应创建一个可信的服务终结点，该终结点只向应用程序的授权用户颁发访问令牌。 如果将用户访问令牌缓存到备份存储，强烈建议你使用加密。

### <a name="access-token-scopes"></a>访问令牌作用域

可通过作用域指定用户访问令牌能够授权的确切 Azure 通信服务功能。 作用域应用于单个用户访问令牌。 Azure 通信服务支持以下用户访问令牌作用域：

| 名称   | 说明                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | 授予加入聊天的能力                                         |
| `VoIP` | 授予使用呼叫客户端库发起和接收 VOIP 呼叫的能力* |
| `Pstn` | 授予使用呼叫客户端库进行 PSTN 呼叫的能力*           |

\* 功能尚不受支持，将很快推出

如果希望取消用户访问某些特定功能的能力，应先[撤销任何现有访问令牌](#revoke-user-access-tokens)，这些令牌可能包含不需要的作用域，然后再颁发一个作用域更小的新令牌。
