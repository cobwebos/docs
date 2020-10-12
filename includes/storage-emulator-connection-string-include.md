---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070535"
---
Azurite 支持单一固定的帐户和众所周知的用于共享密钥身份验证的身份验证密钥。 此帐户和密钥是允许用于 Azurite 的唯一共享密钥凭据。 它们分别是：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Azurite 支持的身份验证密钥仅用于测试客户端身份验证代码的功能。 它没有任何安全用途。 不能在 Azurite 中使用生产存储帐户和密钥。 不应将开发帐户用于生产数据。
> 
> Azurite 仅支持通过 HTTP 进行连接。 但是，若要访问生产性 Azure 存储帐户中的资源，建议使用 HTTPS 协议。
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>使用快捷方式连接到模拟器帐户
从应用程序连接到 Azurite 的最简单方法是在应用程序的配置文件中配置一个引用快捷方式 `UseDevelopmentStorage=true` 的连接字符串。 以下是 *app.config* 文件中连接对象为 Azurite 的连接字符串的示例： 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>使用从众所周知的帐户名称和密钥连接到存储模拟器
要创建引用存储模拟器帐户名称和密钥的连接字符串，必须在连接字符串中希望从模拟器中使用的每个服务指定终结点。 这是必须的，这样连接字符串将引用与生产存储帐户中的终结点不同的模拟器终结点。 例如，连接字符串的值将如下所示：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

此值等同于如上所示的快捷方式 `UseDevelopmentStorage=true`。
