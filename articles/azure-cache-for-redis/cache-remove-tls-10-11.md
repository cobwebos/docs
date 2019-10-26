---
title: 删除使用适用于 Redis 的 Azure 缓存的 TLS 1.0 和 1.1 |Microsoft Docs
description: 了解如何在与 Azure Cache for Redis 通信时从应用程序中删除 TLS 1.0 和1。1
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901893"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>删除使用适用于 Redis 的 Azure 缓存的 TLS 1.0 和1。1

使用 TLS 1.2 或更高版本时，有行业范围内的推送。 已知 TLS 版本1.0 和1.1 很容易受到攻击（例如 BEAST 和贵宾）的攻击，并具有其他常见漏洞和披露（CVE）漏洞。 它们还不支持 PCI 合规性标准推荐的新式加密方法和密码套件。 此[TLS 安全博客](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)详细说明了其中一些漏洞。

尽管这两种方式都不会带来任何问题，但仍应考虑尽快使用 TLS 1.0 和1.1。 在2020年3月31日开始，适用于 Redis 的 Azure 缓存将停止支持这些 TLS 版本。 你的应用程序将需要至少使用 TLS 1.2，以便在此日期后与缓存通信。

本文提供了有关如何从应用程序检测并删除这些依赖项的一般指导。

## <a name="check-if-your-application-is-already-compliant"></a>检查应用程序是否已兼容

若要确定应用程序是否适用于 TLS 1.2，最简单的方法是在它所使用的测试或暂存缓存上设置 tls 1.2 的最小 TLS 版本。 可以在 Azure 门户的缓存实例的[高级设置](cache-configure.md#advanced-settings)中找到最小 TLS 版本设置。 如果在此更改后，应用程序继续按预期方式运行，则很可能是相容的。 应用程序使用的某些 Redis 客户端库可能需要专门配置为启用 TLS 1.2，以便通过该安全协议连接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>将应用程序配置为使用 TLS 1。2

大多数应用程序使用 Redis 客户端库来处理与其缓存的通信。 下面是有关如何在各种编程语言和框架中配置一些常用的客户端库以使用 TLS 1.2 的说明。

### <a name="net-framework"></a>.NET Framework

默认情况下，Redis .NET 客户端在4.6 或更高版本上 .NET Framework 4.5.2 或更高版本上使用最低 TLS 版本。 如果使用的是较旧版本的 .NET Framework，则可以手动启用 TLS 1.2：

* Stackexchange.redis. Redis：在连接字符串中设置 `ssl=true` 和 `sslprotocls=tls12`。
* ServiceStack. Redis：遵循[这些说明](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)。

### <a name="net-core"></a>.NET Core

默认情况下，Redis .NET Core 客户端使用最高 TLS 版本。

### <a name="java"></a>Java

Redis Java 客户端在 Java 版本6或更低版本上使用 TLS 1.0。 如果在缓存上禁用了 TLS 1.0，则 Jedis、番茄酱和 Radisson 将无法连接到 Azure Cache for Redis。 目前没有已知的解决方法。

在 Java 7 或更高版本中，默认情况下，Redis 客户端不使用 TLS 1.2，但可以对其进行配置。 番茄酱和 Radisson 不支持此权利。 如果缓存仅接受 TLS 1.2 连接，它们将中断。 通过 Jedis，可以通过以下代码段指定基础 TLS 设置：

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

PHP 7 上的 Predis 不起作用，因为后者仅支持 TLS 1.0。 在 PHP 7.2.1 或更低的情况下，Predis 在默认情况下使用 TLS 1.0 或1.1。 在实例化客户端时，可以指定 TLS 1.2：

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
