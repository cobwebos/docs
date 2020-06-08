---
title: 删除与 Azure Cache for Redis 配合使用的 TLS 1.0 和 1.1
description: 了解如何在与 Azure Cache for Redis 通信时从应用程序中删除 TLS 1.0 和 1.1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: b7b3556896f2d8bb8fea7ffc4543356e248df60d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848815"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>删除与 Azure Cache for Redis 配合使用的 TLS 1.0 和 1.1

传输层安全性 (TLS) 版本 1.2 或更高版本在行业范围内实现了专用。 已知 TLS 版本 1.0 和 1.1 很容易受到诸如 BEAST 和 POODLE 之类的攻击，并具有其他常见漏洞和披露 (CVE) 缺点。 它们还不支持支付卡行业 (PCI) 符合性标准推荐的新式加密方法和密码套件。 此 [TLS 安全性博客](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)详细说明了其中一些漏洞。

作为此项工作的一部分，我们将对 Azure Cache for Redis 进行以下更改：

* 第 1 阶段：对于新创建的缓存实例（以前为 TLS 1.0），我们会将默认的最低 TLS 版本配置为 1.2。  目前，不会更新现有的缓存实例。 如果需要，你可以将[最低 TLS 版本](cache-configure.md#access-ports)更改为 1.0 或1.1，以实现向后兼容性。 此更改可以通过 Azure 门户或其他管理 API 来完成。
* 第 2 阶段：我们将停止对 TLS 版本 1.0 和 1.1 的支持。 完成此更改后，应用程序将需要使用 TLS 1.2 或更高版本与缓存进行通信。

另外，作为此更改的一部分，我们将删除对较旧的、不安全的加密套件的支持。  如果缓存配置为最低 TLS 版本 1.2，则受支持的加密套件将受到以下限制。

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

本文提供了有关如何检测这些早期 TLS 版本的依赖项并将其从应用程序中删除的一般指导。

这些更改生效的日期如下：

| 云                | 第 1 阶段开始日期 | 第 2 阶段开始日期      |
|----------------------|--------------------|-------------------------|
| Azure（全球）       |  2020 年 1 月 13 日  | 2020 年 5 月 11 日            |
| Azure Government     |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |
| Azure 德国        |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |
| Azure 中国世纪互联 |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |

## <a name="check-whether-your-application-is-already-compliant"></a>检查应用程序是否兼容

确定应用程序是否可以使用 TLS 1.2 的最简单方法是，在其使用的测试或暂存缓存上将“最低 TLS 版本”值设置为 TLS 1.2。 “最低 TLS 版本”设置位于 Azure 门户中缓存实例的[高级设置](cache-configure.md#advanced-settings)中。 如果在此更改后，应用程序继续按预期方式工作，则可能兼容。 你可能需要将应用程序使用的某些 Redis 客户端库配置为专门用于启用 TLS 1.2，以便它们可以通过该安全协议连接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>将应用程序配置为使用 TLS 1.2

大多数应用程序使用 Redis 客户端库来处理与其缓存的通信。 下面是有关如何配置一些常用的客户端库（采用各种编程语言和框架）以使用 TLS 1.2 的说明。

### <a name="net-framework"></a>.NET Framework

默认情况下，Redis .NET 客户端在 .NET Framework 4.5.2 或更早版本上使用最早的 TLS 版本，在 .NET Framework 4.6 或更高版本上使用最新的 TLS 版本。 如果使用的是较旧版本的 .NET Framework，则可以手动启用 TLS 1.2：

* **StackExchange.Redis：** 在连接字符串中设置 `ssl=true` 和 `sslprotocols=tls12`。
* **ServiceStack.Redis：** 请按照 [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) 说明操作，并至少要求 ServiceStack.Redis v5.6。

### <a name="net-core"></a>.NET Core

Redis .NET Core 客户端默认为操作系统默认 TLS 版本，此版本明显取决于操作系统本身。 

操作系统 TLS 版本可能会完全不同，具体取决于操作系统的发布时间以及是否任何其他修补程序更改了默认 TLS 版本。 虽然没有相关的完整信息，但特别是对于 Windows 操作系统，可以在[此处](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)找到详细信息。 

但是，如果你使用的是旧操作系统，或者只是想要确保我们建议通过客户端手动配置首选 TLS 版本。


### <a name="java"></a>Java

Redis Java 客户端基于 Java 版本 6 或更早版本使用 TLS 1.0。 如果在缓存上禁用了 TLS 1.0，则 Jedis、Lettuce 和 Redisson 无法连接到 Azure Cache for Redis。 升级 Java 框架以使用新的 TLS 版本。

对于 Java 7，默认情况下，Redis 客户端不使用 TLS 1.2，但可以对其进行配置。 Jedis 允许你使用以下代码片段指定基础 TLS 设置：

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Lettuce 和 Redisson 客户端尚不支持指定 TLS 版本，因此，如果缓存仅接受 TLS 1.2 连接，它们将中断。 我们正在审查针对这些客户端的修复程序，因此请在支持的情况下与这些软件包一起检查以获取更新后的版本。

在 Java 8 中，默认使用 TLS 1.2，并且在大多数情况下都不需要更新客户端配置。 为了安全起见，请测试你的应用程序。

### <a name="nodejs"></a>Node.js

节点 Redis 和 IORedis 默认使用 TLS 1.2。

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* 低于 PHP 7 的版本：Predis 仅支持 TLS 1.0。 这些版本不适用于 TLS 1.2；必须升级才能使用 TLS 1.2。
 
* PHP 7.0 到 PHP 7.2.1：默认情况下，Predis 仅使用 TLS 1.0 或 1.1。 你可以使用以下解决方法来使用 TLS 1.2。 创建客户端实例时指定 TLS 1.2：

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 及更高版本：Predis 使用最新的 TLS 版本。

#### <a name="phpredis"></a>PhpRedis

PhpRedis 在任何 PHP 版本上均不支持 TLS。

### <a name="python"></a>Python

Redis-py 默认使用 TLS 1.2。

### <a name="go"></a>GO

Redigo 默认使用 TLS 1.2。

## <a name="additional-information"></a>其他信息

- [如何配置 Azure Cache for Redis](cache-configure.md)
