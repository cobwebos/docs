---
title: 删除 TLS 1.0 和1.1，使其与用于 Redis 的 Azure 缓存一起使用
description: 了解如何在与 Azure Cache for Redis 通信时从应用程序中删除 TLS 1.0 和1。1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812838"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>删除 TLS 1.0 和1.1，使其与用于 Redis 的 Azure 缓存一起使用

传输层安全性（TLS）版本1.2 或更高版本的专用使用是行业范围内的推送。 已知 TLS 版本1.0 和1.1 很容易受到攻击（例如 BEAST 和贵宾）的攻击，并具有其他常见漏洞和披露（CVE）漏洞。 它们还不支持支付卡行业（PCI）符合性标准推荐的新式加密方法和密码套件。 此[TLS 安全博客](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)更详细地介绍了其中一些漏洞。

尽管这些注意事项都不会引起直接问题，但我们建议你尽快停止使用 TLS 1.0 和1.1。 适用于 Redis 的 Azure 缓存将在2020年3月31日停止支持这些 TLS 版本。 在此日期之后，你的应用程序将需要使用 TLS 1.2 或更高版本与缓存通信。

本文提供了有关如何检测这些早期 TLS 版本的依赖项并将其从应用程序中删除的一般指导。

## <a name="check-whether-your-application-is-already-compliant"></a>检查应用程序是否已兼容

了解应用程序是否适用于 TLS 1.2 的最简单方法是在它所使用的测试或暂存缓存上将**最小 tls 版本**值设置为 tls 1.2。 **最小 TLS 版本**设置位于 Azure 门户的缓存实例的[高级设置](cache-configure.md#advanced-settings)中。 如果在此更改后，应用程序继续按预期方式工作，则可能符合要求。 你可能需要将应用程序使用的某些 Redis 客户端库专门用于启用 TLS 1.2，以便它们可以通过该安全协议连接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>将应用程序配置为使用 TLS 1。2

大多数应用程序使用 Redis 客户端库来处理与其缓存的通信。 下面是有关如何配置一些常用的客户端库（在各种编程语言和框架中）以使用 TLS 1.2 的说明。

### <a name="net-framework"></a>.NET Framework

默认情况下，Redis .NET 客户端在 .NET Framework 4.5.2 或更早版本上使用最早的 TLS 版本，并在 .NET Framework 4.6 或更高版本上使用最新的 TLS 版本。 如果使用的是较旧版本的 .NET Framework，则可以手动启用 TLS 1.2：

* **Stackexchange.redis. Redis：** 在连接字符串中设置 `ssl=true` 和 `sslprotocols=tls12`。
* **ServiceStack. Redis：** 按照[ServiceStack. Redis 说明](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)操作。

### <a name="net-core"></a>.NET Core

默认情况下，Redis .NET Core 客户端使用最新的 TLS 版本。

### <a name="java"></a>Java

Redis Java 客户端使用 Java 版本6或更早版本上的 TLS 1.0。 如果在缓存上禁用了 TLS 1.0，则 Jedis、番茄酱和 Radisson 不能连接到 Azure Cache for Redis。 目前没有已知的解决方法。

在 Java 7 或更高版本中，默认情况下，Redis 客户端不使用 TLS 1.2，但可以对其进行配置。 番茄酱和 Radisson 目前不支持此配置。 如果缓存仅接受 TLS 1.2 连接，它们将中断。 通过 Jedis，可以通过以下代码段指定基础 TLS 设置：

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Node Redis 和 IORedis 在默认情况下使用 TLS 1.2。

### <a name="php"></a>PHP

PHP 7 上的 Predis 不起作用，因为 PHP 7 仅支持 TLS 1.0。 在 PHP 7.2.1 或更早版本中，Predis 默认使用 TLS 1.0 或1.1。 您可以在创建客户端实例时指定 TLS 1.2：

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

在 PHP 7.3 或更高版本上，Predis 使用最新的 TLS 版本。

PhpRedis 不支持任何 PHP 版本上的 TLS。

### <a name="python"></a>Python

默认情况下，Redis py 使用 TLS 1.2。

### <a name="go"></a>前往

默认情况下，Redigo 使用 TLS 1.2。

## <a name="additional-information"></a>其他信息

- [如何为 Redis 配置 Azure 缓存](cache-configure.md)
