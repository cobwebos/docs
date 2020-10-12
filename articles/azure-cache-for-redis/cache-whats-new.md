---
title: Azure Cache for Redis 的新增功能
description: 适用于 Redis 的 Azure 缓存的最新更新
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492385"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis 的新增功能

## <a name="azure-tls-certificate-change"></a>Azure TLS 证书更改

Microsoft 正在更新 Azure 服务，以使用不同的一组证书颁发机构 (Ca) 中的 TLS 服务器证书。 此更改按8月13日（2020到10月26日）的阶段推出，2020 (估计) 。 Azure 正在进行此更改，因为 [当前 ca 证书不符合某个 ca/浏览器论坛基线要求](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)。 此问题已在2020年7月1日报告，适用于世界各地 (PKI) 提供商的多个常用公钥基础结构。 目前，Azure 服务使用的大多数 TLS 证书来自 *巴尔的摩 CyberTrust 根* PKI。 适用于 Redis 的 Azure 缓存服务将继续链接到巴尔的摩 CyberTrust Root。 但是，其 TLS 服务器证书将由2020年10月12日开始 (ICAs) 的新中间证书颁发机构颁发。

> [!NOTE]
> 此更改仅限于公共 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/)中的服务。 它不包括主权 (例如，中国) 或政府云。
>
>

### <a name="does-this-change-affect-me"></a>此更改是否会对我产生影响？

我们预计，Redis 客户的大多数 Azure 缓存不受此更改的影响。 如果你的应用程序显式指定了可接受证书的列表（一种称为 "证书固定" 的做法），则可能会影响你的应用程序。 如果已将其固定到中介或叶证书而不是巴尔的摩 CyberTrust 根中，则应 **立即采取措施** 来更改证书配置。

下表提供了有关正在被回滚的证书的信息。 根据应用程序使用的证书，可能需要对其进行更新，以防丢失到 Azure Cache for Redis 实例的连接。

| CA 类型 | 当前 | Post (10 月12日，2020)  | 操作 |
| ----- | ----- | ----- | ----- |
| Root | 指纹： d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 过期日期：星期一，5月12日，2025，4:59:00 PM<br><br> 使用者名称：<br> CN = 巴尔的摩 CyberTrust Root<br> OU = CyberTrust<br> O = 巴尔的摩<br> C = IE | 未更改 | 无 |
| 中介 | 指纹<br> CN = Microsoft IT TLS CA 1<br> 指纹：417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> 指纹：54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> 指纹：8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> 指纹： Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 过期时间：星期五，2024年5月20日，5:52:38 AM<br><br> 使用者名称：<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = 华盛顿<br> C = 美国<br> | 指纹<br> CN = Microsoft RSA TLS CA 01<br> 指纹：703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 指纹： b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 过期时间：星期二，十月8，2024 12:00:00 AM;<br><br> 使用者名称：<br> O = Microsoft Corporation<br> C = 美国<br> | 必须 |

### <a name="what-actions-should-i-take"></a>我应该采取什么措施？

如果你的应用程序使用操作系统证书存储或将巴尔的摩 root 固定在其他应用程序中，则无需执行任何操作。 另一方面，如果应用程序需要固定任何中间或叶 TLS 证书，则建议你固定以下根：

| Certificate | Thumbprint |
| ----- | ----- |
| [巴尔的摩根 CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert 全局根 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 中间证书和叶证书都应经常更改。 建议不要对它们进行依赖。 请将应用程序固定到根证书，因为它的滚动频率更低。
>
>

若要继续固定中间证书，请将以下内容添加到 "固定中间证书" 列表中，其中包含一些其他内容，以最大程度地减少将来更改：

| CA 的公用名 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS 发证 CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 发证 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 发证 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 发证 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

如果你的应用程序在代码中验证证书，你将需要修改它以识别新固定证书的属性 (例如，颁发者、指纹) 。 这一额外验证应涵盖所有固定的证书，以便更好地进行验证。

## <a name="next-steps"></a>后续步骤

如果你有其他问题，请通过 [支持](https://azure.microsoft.com/support/options/)联系我们。  
