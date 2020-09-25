---
title: 适用于 Azure SQL 数据库 & SQL 托管实例的证书轮换
description: 了解将会影响 Azure SQL 数据库和 Azure SQL 托管实例的根证书更改即将发生的更改
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: a273b9aaae083bb4566d289e9680b50c686d4e9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341489"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>了解 Azure SQL 数据库 & SQL 托管实例的根 CA 更改的更改

Azure SQL 数据库 & SQL 托管实例将更改启用了 SSL 的客户端应用程序/驱动程序的根证书，用于建立安全的 TDS 连接。 [当前根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)设置为在标准维护和安全最佳方案中的2020年10月26日过期。 本文提供了有关即将发生的更改、将受影响的资源以及确保你的应用程序保持与数据库服务器的连接所需的步骤的更多详细信息。

## <a name="what-update-is-going-to-happen"></a>要进行哪些更新？

[证书颁发机构 (ca) 浏览器论坛](https://cabforum.org/) 最近发布的多个证书发布的报表，这些证书不符合要求。

根据行业的合规性要求，CA 供应商开始为不符合的 Ca 收回 CA 证书，要求服务器使用由合规 Ca 颁发的证书，并由这些符合的 ca 颁发的 CA 证书进行签名。 由于 Azure SQL 数据库 & SQL 托管实例当前使用其中一个不符合的证书，客户端应用程序使用这些证书来验证其 SSL 连接，因此，我们需要确保 (下面所述的适当操作) 以尽量降低对 Azure SQL 服务器的潜在影响。

新证书将于2020年10月26日开始使用。 如果在从 SQL 客户端连接 (TrustServerCertificate = true) 时使用完全验证服务器证书，则需要确保 SQL 客户端能够在2020年10月26日之前验证新的根证书。

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>如何实现知道我的应用程序可能会受到影响吗？

使用 SSL/TLS 并验证根证书的所有应用程序都需要更新根证书，以便连接到 Azure SQL 数据库 & SQL 托管实例。 

如果当前未使用 SSL/TLS，则不会影响应用程序的可用性。 可以通过查看连接字符串来验证客户端应用程序是否正在尝试验证根证书。 如果 TrustServerCertificate 显式设置为 true，则不会受到影响。

如果你的客户端驱动程序使用操作系统证书存储区，而大多数驱动程序都是，并且你的操作系统会定期维护，则此更改很可能不会影响你，因为我们要切换到的根证书应已在受信任的根证书存储中可用。 检查巴尔的摩 CyberDigiCert GlobalRoot G2 并验证它是否存在。

如果你的客户端驱动程序使用本地文件证书存储，以避免应用程序的可用性因为证书被意外撤销而被中断，或者若要更新已吊销的证书，请参阅 [**维护连接所需的操作**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) 部分。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>维护连接需要执行哪些操作

若要避免应用程序的可用性因为证书被意外撤销而被中断，或要更新已吊销的证书，请执行以下步骤：

*   从以下链接下载巴尔的摩 CyberTrust Root & DigiCert GlobalRoot G2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同时包含 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 证书的组合 CA 证书存储区。

## <a name="what-can-be-the-impact"></a>影响有哪些？
如果正在按此处所述验证服务器证书，则可能会中断应用程序的可用性，因为无法访问数据库。 根据你的应用程序，你可能会收到各种错误消息，其中包括但不限于：
*   证书/已吊销的证书无效
*   连接超时
*   错误（如果适用）

## <a name="frequently-asked-questions"></a>常见问题

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>如果我不使用 SSL/TLS，是否仍需要更新根 CA？
如果不使用 SSL/TLS，则不需要有关此更改的任何操作。 仍应为开始使用最新的 TLS 版本制定计划，因为我们会在不久的将来规划 TLS 强制。

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>如果在2020年10月26日之前未更新根证书，会发生什么情况？
如果在2020年11月30日之前未更新根证书，则通过 SSL/TLS 连接并对根证书进行验证的应用程序将无法与 Azure SQL 数据库进行通信，& SQL 托管实例并且应用程序将遇到与 SQL 托管实例的 Azure SQL & 数据库的连接问题。

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>是否需要为此更改计划维护停机时间？<BR>
否。 由于此处的更改仅在客户端连接到服务器，因此，此处不需要维护停机时间进行此更改。

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>如果在2020年10月26日之前无法获得此更改的计划停机情况，该怎么办？
由于用于连接到服务器的客户端需要更新证书信息（如 [此处](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)的修复部分所述），因此在这种情况下，服务器不需要停机。

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>如果我在2020年11月30日之后创建一个新的服务器，我会受到影响吗？
对于在2020年10月26日之后创建的服务器，你可以使用应用程序的新颁发的证书来使用 SSL 进行连接。

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft 更新其证书的频率或过期策略是什么？
Azure SQL 数据库 & SQL 托管实例使用的这些证书由受信任的证书颁发机构 (CA) 提供。 因此，Azure SQL 数据库 & SQL 托管实例上的这些证书支持受 CA 的支持。 但是，在这种情况下，这些预定义证书中可能存在无法预料的错误，这些错误需要在最早进行修复。

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>如果我使用的是读取副本，是否只需在主服务器或所有读取副本上执行此更新？
由于此更新是客户端更改，因此，如果客户端用于从副本服务器读取数据，则还需要对这些客户端应用更改。 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>是否有服务器端查询来验证是否正在使用 SSL？
由于此配置是客户端，因此不能在服务器端使用信息。

### <a name="what-if-i-have-further-questions"></a>如果我有其他问题该怎么办？
如果你有支持计划并需要技术帮助，请创建 Azure 支持请求，请参阅 [如何创建 azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。
