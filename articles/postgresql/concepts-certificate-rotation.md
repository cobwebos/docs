---
title: Azure Database for PostgreSQL 单一服务器的证书轮换
description: 了解将影响 Azure Database for PostgreSQL 单一服务器的根证书更改的即将发生的更改
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: ce1ef0257f2003c3af01d71b7da3924ebd9ed44f
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603911"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>了解 Azure Database for PostgreSQL 单一服务器的根 CA 更改的更改

Azure Database for PostgreSQL 将更改启用了 SSL 的客户端应用程序/驱动程序的根证书，用于 [连接到数据库服务器](concepts-connectivity-architecture.md)。 当前可用的根证书已设置为在标准维护和安全最佳方案中的 2020 (10/26/2020) 。 本文提供了有关即将发生的更改、将受影响的资源以及确保你的应用程序保持与数据库服务器的连接所需的步骤的更多详细信息。

## <a name="what-update-is-going-to-happen"></a>要进行哪些更新？

在某些情况下，应用程序使用从受信任的证书颁发机构生成的本地证书文件 (CA) 证书文件进行安全连接。 目前，客户只能使用预定义的证书来连接到位于 [此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 Azure Database for PostgreSQL 服务器。 但是， [证书颁发机构 (ca) 浏览器论坛](https://cabforum.org/) 最近发布的多个证书的报告，这些证书由 CA 供应商颁发为不合规。

根据行业的合规性要求，CA 供应商开始为不符合的 Ca 收回 CA 证书，要求服务器使用由合规 Ca 颁发的证书，并由这些符合的 ca 颁发的 CA 证书进行签名。 由于 Azure Database for PostgreSQL 当前使用其中一个不符合的证书，客户端应用程序使用这些证书来验证其 SSL 连接，因此，我们需要确保 (下面所述的适当操作) ，以最大程度地减少对 PostgreSQL 服务器造成的潜在影响。

新证书将于2020年10月26日开始使用 (10/26/2020) 。 如果在通过 PostgreSQL 客户端连接时使用 CA 验证或完全验证服务器证书 (sslmode = verify-CA 或 sslmode = verify-full) ，则需要在 2020 (10/26/2020) 之前更新应用程序配置。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何实现知道数据库是否会受到影响？

使用 SSL/TLS 并验证根证书的所有应用程序都需要更新根证书。 通过查看连接字符串，可以确定连接是否验证根证书。
-   如果连接字符串包含 `sslmode=verify-ca` 或 ""
-   如果连接字符串包含 `sslmode=disable` ，则无需更新证书。
-   如果连接字符串包含 `sslmode=allow` 、 `sslmode=prefer` 或 `sslmode=require` ，则无需更新证书。 
-   如果连接字符串不是特定的 sslmode，则无需更新证书。

如果你使用的客户端将连接字符串提取出来，请查看客户端的文档，以了解它是否验证证书。

若要了解 PostgreSQL sslmode，请参阅 PostgreSQL 文档中的 [SSL 模式说明](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) 。

若要避免应用程序的可用性因为证书被意外撤销而被中断，或要更新已吊销的证书，请参阅 [**"维护连接需要执行哪些操作"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) 部分。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>维护连接需要执行哪些操作

若要避免应用程序的可用性因为证书被意外撤销而被中断，或要更新已吊销的证书，请执行以下步骤。 其思路是创建一个新的 *pem* 文件，该文件结合了当前证书和新证书，并在 SSL 证书验证期间将使用允许的值。 请参阅以下步骤：

*   从以下链接下载 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   同时包含 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 证书的组合 CA 证书存储区。
    *   对于 Java (使用 DefaultJavaSSLFactory 的 PostgreSQL JDBC) 用户执行以下操作：

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然后，将原始密钥存储文件替换为新生成的密钥存储文件：
        *   System.object ( "trustStore"、"path_to_truststore_file" ) ; 
        *   System.web ( "trustStorePassword"，"password" ) ;
        
    *   对于 Windows 上的 .NET (Npgsql) 用户，请确保 **巴尔的摩 CyberTrust root** 和 **DigiCert Global root G2** 都存在于 Windows 证书存储和受信任的根证书颁发机构中。 如果不存在任何证书，则导入缺少的证书。

        ![Azure Database for PostgreSQL .net 证书](media/overview/netconnecter-cert.png)

    *   对于使用 SSL_CERT_DIR 的 Linux 上的 .NET (Npgsql) 用户，请确保 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于 SSL_CERT_DIR 指示的目录中。 如果任何证书不存在，请创建缺少的证书文件。

    *   对于其他 PostgreSQL 客户端用户，你可以按以下格式合并两个 CA 证书文件（如下所示）

        </br>-----开始证书-----
 </br> (根 CA1： Baltimorecybertrustroot.crt.pem) 
 </br>-----结束证书-----
 </br>-----开始证书-----
 </br> (根 CA2： DigiCertGlobalRootG2) 
 </br>-----结束证书-----

*   将原始根 CA pem 文件替换为组合根 CA 文件，然后重新启动应用程序/客户端。
*   将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>不更新证书会产生什么影响？
如果使用巴尔的摩 CyberTrust 根证书来验证 Azure Database for PostgreSQL 如此处所述的 SSL 连接，则应用程序的可用性可能会中断，因为数据库将无法访问。 根据你的应用程序，你可能会收到各种错误消息，其中包括但不限于：
*   证书/已吊销的证书无效
*   连接超时

## <a name="frequently-asked-questions"></a>常见问题

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我不使用 SSL/TLS，是否仍需要更新根 CA？
如果不使用 SSL/TLS，则无需执行任何操作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我正在使用 SSL/TLS，是否需要重新启动数据库服务器来更新根 CA？
不需要，你无需重新启动数据库服务器即可开始使用新证书。 这是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. 如果在2020年10月26日之前未更新根证书，则会发生什么情况 (10/26/2020) ？
如果在2020年10月26日之前未更新根证书，则通过 SSL/TLS 连接并对根证书进行验证的应用程序将无法与 PostgreSQL 数据库服务器通信，应用程序将遇到 PostgreSQL 数据库服务器的连接问题。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. 如果将应用服务用于 Azure Database for PostgreSQL，会产生什么影响？
对于 Azure 应用服务，连接到 Azure Database for PostgreSQL，可以有两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。
*   已将此新证书添加到应用服务中的平台级别。 如果你在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。
*   如果你在代码中显式包含 SSL 证书文件的路径，则需要下载新证书，并更新代码以使用新证书。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. 如果使用 Azure Kubernetes Services (AKS) 与 Azure Database for PostgreSQL，会产生什么影响？
如果尝试使用 Azure Kubernetes Services (AKS) 连接到 Azure Database for PostgreSQL，则与从专用客户主机环境访问类似。 请参阅 [此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. 如果使用 Azure 数据工厂连接到 Azure Database for PostgreSQL，会产生什么影响？
对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

如果使用自承载 Integration Runtime 在连接字符串中显式包含 SSL 证书文件的路径，则需要下载 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. 是否需要为此更改计划数据库服务器维护停机时间？
不是。 由于此处的更改仅在客户端连接到数据库服务器，因此数据库服务器不需要维护停机时间来进行此更改。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>8. 如果在 2020 (10/26/2020) 之前无法获得此更改的计划停机时间怎么办？
由于用于连接到服务器的客户端需要更新证书信息（如 [此处](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修复部分所述），因此在这种情况下，服务器不需要停机。

### <a name="9-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>9. 如果我在2020年10月26日之后创建一个新的服务器，我会受到影响吗？
对于10月26日之后创建的服务器 2020 (10/26/2020) ，你可以使用应用程序的新颁发的证书来使用 SSL 进行连接。

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Microsoft 更新其证书的频率或过期策略是什么？
Azure Database for PostgreSQL 使用的这些证书由受信任的证书颁发机构 (CA) 提供。 因此，Azure Database for PostgreSQL 上对这些证书的支持与 CA 的支持这些证书相关联。 但是，在这种情况下，这些预定义证书中可能存在无法预料的错误，这些错误需要在最早进行修复。

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-the-read-replicas"></a>11. 如果我使用的是读取副本，是否只需在主服务器或读取副本上执行此更新？
由于此更新是客户端更改，因此，如果客户端用于从副本服务器读取数据，则还需要对这些客户端应用更改。 

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. 我们是否有服务器端查询来验证是否正在使用 SSL？
若要验证是否正在使用 SSL 连接连接到服务器，请参阅 [ssl 验证](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)。

### <a name="13-what-if-i-have-further-questions"></a>13. 如果我有其他问题，该怎么办？
如果有疑问，请从 [Microsoft Q&的](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)社区专家那里获取答案。 如果你有支持计划并需要技术  [帮助，请联系我们](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)